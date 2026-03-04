# Understanding: power-management

> WakeLock usage and screen wake strategy for Android device

## Phase: EXPLORING

## Hypothesis

Uses PowerManager.WakeLock to wake device screen when bringing app to foreground.
Expected to find: WakeLock acquisition, timeout handling, power management best practices.

## Sources

- android/app/src/main/java/.../ForegroundBackgroundModule.java (lines 70-81)

## Validated Understanding

**WakeLock Implementation:**

```java
PowerManager.WakeLock wl = mPowerManager.newWakeLock(
    PowerManager.ACQUIRE_CAUSES_WAKEUP | 
    PowerManager.ON_AFTER_RELEASE | 
    PowerManager.FULL_WAKE_LOCK, 
    "incoming_call"
);
wl.acquire(10000);  // 10 second timeout
```

**Key Findings:**

1. **Lock Type**: FULL_WAKE_LOCK
   - Deprecated since API level 20 (Android 6.0)
   - Modern alternative: PARTIAL_WAKE_LOCK with WindowManager flags
   - Still works on newer Android versions but not recommended

2. **Flags**:
   - ACQUIRE_CAUSES_WAKEUP: Turns on screen immediately
   - ON_AFTER_RELEASE: Keeps screen on briefly after release
   - FULL_WAKE_LOCK: Full screen brightness and keyboard backlight

3. **Timeout**: 10 seconds (10000ms)
   - Prevents battery drain from forgotten WakeLock
   - May be insufficient for slow devices or heavy operations
   - No mechanism to extend if needed

4. **Tag**: "incoming_call"
   - Used for debugging WakeLock history
   - Confirms use case: incoming call screen

5. **Issues**:
   - **DEPRECATED API**: FULL_WAKE_LOCK should be replaced
   - **FIXED TIMEOUT**: No dynamic adjustment based on operation completion
   - **NO RELEASE CALLBACK**: Can't know when WakeLock is released

## Children Identified

> Deeper concepts spawned during SPAWNING phase

| Child | Hypothesis | Status |
|-------|------------|--------|
| - | - | - |

## Dependencies

- **Uses**: Android PowerManager API
- **Used by**: foreground-management (toForeground operation)

## Key Insights

1. Uses deprecated API that should be modernized
2. Timeout-based release (not completion-based)
3. Designed for incoming call use case
4. No battery optimization considerations

## ADR Candidates

- WakeLock type selection (FULL vs PARTIAL + WindowManager)
- Timeout duration (10 seconds)
- Tag naming for debugging

## Flow Recommendation

- **Type**: SDD
- **Confidence**: high
- **Rationale**: Internal Android implementation detail

## Synthesis

> Updated during SYNTHESIZING phase after children complete

### From Children
[No children - leaf node]

### Combined Understanding

Power management uses deprecated WakeLock API. Should be modernized to:
- Use PARTIAL_WAKE_LOCK instead of FULL_WAKE_LOCK
- Add WindowManager.LayoutParams for screen brightness
- Implement completion-based release instead of fixed timeout

## Bubble Up

> Summary to pass to parent during EXITING

- Uses deprecated FULL_WAKE_LOCK (should use PARTIAL + WindowManager)
- 10 second timeout may be insufficient
- Tagged for "incoming_call" use case
- No completion-based release mechanism

---

*Phase: EXPLORING | Depth: 2 | Parent: foreground-management*
