# Understanding: foreground-management

> Power management and screen wake logic for bringing app to foreground

## Phase: SYNTHESIZING

## Hypothesis

This domain handles Android-specific foreground/background switching using:
- PowerManager.WakeLock to wake the screen
- Intent flags to launch MainActivity from background
- HandlerThread for background processing

## Sources

- android/app/src/main/java/.../ForegroundBackgroundModule.java - toForeground() implementation
- src/ForegroundBackground.js - JavaScript API

## Validated Understanding

**toForeground() Implementation Analysis:**

1. **Activity Launch** (lines 53-66):
   ```java
   String ns = mContext.getPackageName();
   String cls = ns + ".MainActivity";
   Intent intent = new Intent(mContext, Class.forName(cls));
   intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.EXTRA_DOCK_STATE_CAR);
   intent.addCategory(Intent.CATEGORY_LAUNCHER);
   intent.putExtra("foreground", true);
   mContext.startActivity(intent);
   ```
   - Dynamically constructs MainActivity class name from package
   - Uses FLAG_ACTIVITY_NEW_TASK to start from background
   - EXTRA_DOCK_STATE_CAR flag (unusual - may be for car mode?)
   - Adds CATEGORY_LAUNCHER to appear as launcher activity
   - Passes "foreground" extra flag

2. **Screen Wake** (lines 70-81):
   ```java
   PowerManager.WakeLock wl = mPowerManager.newWakeLock(
       PowerManager.ACQUIRE_CAUSES_WAKEUP | 
       PowerManager.ON_AFTER_RELEASE | 
       PowerManager.FULL_WAKE_LOCK, 
       "incoming_call"
   );
   wl.acquire(10000);  // 10 second timeout
   ```
   - Uses FULL_WAKE_LOCK (deprecated in API 20+)
   - ACQUIRE_CAUSES_WAKEUP wakes device from sleep
   - 10 second timeout for safety
   - Tagged as "incoming_call" (suggests use case)

3. **Threading Model**:
   - HandlerThread with THREAD_PRIORITY_FOREGROUND
   - Uses post() to run jobs on worker thread
   - Prevents blocking the React bridge thread

4. **Issues Identified**:
   - **DEPRECATED**: FULL_WAKE_LOCK deprecated since API 20
   - **HARDcoded TIMEOUT**: 10 seconds may not be enough for slow devices
   - **NO ERROR HANDLING**: startActivity() wrapped in try-catch but errors just logged
   - **COMMENTED CODE**: wl.acquire() call is commented out in original code

## Children Identified

> Deeper concepts spawned during SPAWNING phase

| Child | Hypothesis | Status |
|-------|------------|--------|
| power-management | WakeLock usage and screen wake strategy | PENDING |
| activity-launching | Intent construction and activity launch flags | PENDING |

## Dependencies

- **Uses**: native-bridge (exposed via @ReactMethod)
- **Used by**: Application JavaScript (toForeground() call)

## Key Insights

1. Designed for incoming call scenarios (tag names, comments confirm this)
2. Uses aggressive wake strategy (FULL_WAKE_LOCK + ACQUIRE_CAUSES_WAKEUP)
3. Car mode flag suggests automotive use case consideration
4. Worker thread prevents blocking but adds complexity

## ADR Candidates

- WakeLock strategy (FULL_WAKE_LOCK vs modern alternatives)
- Intent flags selection (CAR mode, LAUNCHER category)
- Timeout duration (10 seconds)

## Flow Recommendation

- **Type**: SDD (internal service logic)
- **Confidence**: high
- **Rationale**: Android-specific implementation, no stakeholder docs needed

## Synthesis

> Updated during SYNTHESIZING phase after children complete

### From Children

**power-management**:
- Uses deprecated FULL_WAKE_LOCK (should modernize to PARTIAL + WindowManager)
- 10 second timeout may be insufficient
- No completion-based release mechanism
- Tagged for "incoming_call" use case

**activity-launching**:
- Dynamic class resolution for MainActivity
- EXTRA_DOCK_STATE_CAR flag (automotive use case?)
- CATEGORY_LAUNCHER for background launch
- Minimal error handling (log and continue)
- "foreground" extra for state detection

### Combined Understanding

Foreground management is designed for incoming call scenarios with these characteristics:

**Strengths**:
- Dynamic class resolution avoids hardcoded paths
- "foreground" extra enables state-aware initialization
- Worker thread prevents UI blocking
- Timeout prevents battery drain

**Issues**:
1. **DEPRECATED API**: FULL_WAKE_LOCK should be replaced
2. **CAR MODE**: EXTRA_DOCK_STATE_CAR needs justification
3. **ERROR HANDLING**: Silent failures may cause inconsistent state
4. **TIMEOUT**: Fixed 10s may not suit all devices

**Recommendations**:
- Modernize WakeLock usage
- Improve error handling (fail gracefully)
- Consider completion-based release
- Document CAR mode requirement

## Bubble Up

> Summary to pass to parent during EXITING

- Incoming call use case confirmed (tag names, comments)
- Deprecated WakeLock API needs modernization
- CAR mode flag suggests automotive integration
- Error handling needs improvement
- SDD flow recommended for documentation

---

*Phase: EXITING | Depth: 1 | Parent: root*
