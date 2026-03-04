# Understanding: native-bridge

> React Native bridge connecting JavaScript interface to Android native modules

## Phase: SPAWNING

## Hypothesis

This domain handles the React Native bridge mechanism that allows JavaScript code to call native Android methods. 
Expected to find: NativeModule interface implementation, method annotations (@ReactMethod), module registration.

## Sources

- src/ForegroundBackground.js - JavaScript class exposing toForeground() and toBackground()
- android/app/src/main/java/.../ForegroundBackgroundModule.java - Native module implementation
- android/app/src/main/java/.../ForegroundBackgroundModulePackage.java - Package registration

## Validated Understanding

The native bridge follows standard React Native architecture:

1. **JavaScript Side** (ForegroundBackground.js):
   - Exports a class `ForegroundBackground` (note: also exported as `ReplaceDialer`)
   - Uses `NativeModules.ForegroundBackground` to call native toForeground()
   - Uses `NativeModules.ReplaceDialerModule` to call native toBackground()
   - **Issue**: Inconsistent module names between JS calls (ForegroundBackground vs ReplaceDialerModule)

2. **Native Side** (ForegroundBackgroundModule.java):
   - Extends `ReactContextBaseJavaModule`
   - Implements `toForeground()` with @ReactMethod annotation
   - Implements `toBackground()` with @ReactMethod annotation (currently empty)
   - getName() returns "ForegroundBackgroundModule"
   - Uses HandlerThread for background job processing
   - Uses PowerManager.WakeLock for screen wake functionality

3. **Module Registration** (ForegroundBackgroundModulePackage.java):
   - Implements ReactPackage interface
   - createNativeModules() adds ForegroundBackgroundModule to list
   - createViewManagers() returns empty list (no custom views)

## Children Identified

> Deeper concepts spawned during SPAWNING phase

| Child | Hypothesis | Status |
|-------|------------|--------|
| method-binding | How JS methods map to native methods via @ReactMethod | PENDING |
| module-registration | How native module is registered with React Native | PENDING |

## Dependencies

- **Uses**: package-structure (module registration)
- **Used by**: foreground-management (exposes native functionality)

## Key Insights

1. [pending]
2. [pending]

## ADR Candidates

- [pending]

## Flow Recommendation

- **Type**: SDD (internal service logic, developer-facing API)
- **Confidence**: medium
- **Rationale**: Bridge code is internal infrastructure, no stakeholder documentation needed

## Synthesis

> Updated during SYNTHESIZING phase after children complete

### From Children

**method-binding**: 
- Standard @ReactMethod pattern used correctly
- **BUG**: Module name mismatch - JS expects "ReplaceDialerModule" but native module returns "ForegroundBackgroundModule"
- **BUG**: toBackground() method is empty
- Methods are fire-and-forget (no callbacks/promises)

### Combined Understanding

Native bridge implementation has critical bugs that will cause runtime failures:
1. Module name inconsistency will break toBackground() calls
2. toBackground() has no implementation even if module name was fixed
3. No async completion handling for operations that take time

The bridge follows React Native conventions but needs bug fixes before being functional.

## Bubble Up

> Summary to pass to parent during EXITING

- React Native bridge with standard architecture
- Critical bugs: module name mismatch, empty toBackground() implementation
- Needs TDD flow for testing bridge reliability

---

*Phase: EXITING | Depth: 1 | Parent: root*
