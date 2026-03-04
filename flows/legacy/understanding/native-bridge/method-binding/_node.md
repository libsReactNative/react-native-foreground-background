# Understanding: method-binding

> How JavaScript methods map to native Android methods via @ReactMethod annotation

## Phase: SYNTHESIZING

## Hypothesis

React Native uses @ReactMethod annotation to expose native methods to JavaScript. 
Methods are automatically bridged via reflection. Expected to find:
- @ReactMethod annotated methods in ForegroundBackgroundModule
- Method signatures matching JS calls (toForeground, toBackground)
- Promise-based or callback-based return types

## Sources

- android/app/src/main/java/.../ForegroundBackgroundModule.java - @ReactMethod implementations
- src/ForegroundBackground.js - JavaScript method calls

## Validated Understanding

**Method Binding Analysis:**

1. **Native Methods** (ForegroundBackgroundModule.java):
   ```java
   @ReactMethod
   public void toForeground() {
       Log.d(LOG, "toForeground()");
       // Implementation: wake screen, launch MainActivity
   }

   @ReactMethod
   public void toBackground() {
       // EMPTY - no implementation
   }
   ```

2. **JavaScript Calls** (ForegroundBackground.js):
   ```javascript
   toForeground() {
     return NativeModules.ForegroundBackground.toForeground();
   }

   toBackground() {
     return NativeModules.ReplaceDialerModule.toBackground();
   }
   ```

3. **Key Findings**:
   - Methods use void return type (no callbacks/promises)
   - Module name mismatch: JS calls `NativeModules.ReplaceDialerModule` but native module returns "ForegroundBackgroundModule"
   - toBackground() is empty - needs implementation
   - No error handling or callbacks for async operations
   - Methods are synchronous from JS perspective but perform async operations internally

4. **Issues Identified**:
   - **BUG**: JS expects module name "ReplaceDialerModule" but native module registers as "ForegroundBackgroundModule"
   - **TODO**: toBackground() method has no implementation
   - **DESIGN**: No callbacks for completion/failure states

## Children Identified

> Deeper concepts spawned during SPAWNING phase

| Child | Hypothesis | Status |
|-------|------------|--------|
| - | - | - |

## Dependencies

- **Uses**: React Native bridge mechanism
- **Used by**: Application JavaScript code

## Key Insights

1. [pending]
2. [pending]

## ADR Candidates

- [pending]

## Flow Recommendation

- **Type**: SDD
- **Confidence**: high
- **Rationale**: Internal implementation detail, no stakeholder-facing docs needed

## Synthesis

> Updated during SYNTHESIZING phase after children complete

### From Children
[No children - leaf node]

### Combined Understanding

Method binding uses standard React Native @ReactMethod annotation pattern. Critical issues found:
1. Module name mismatch will cause runtime error for toBackground() call
2. toBackground() has no implementation
3. No async completion handling

This is a simple bridge with bugs that need fixing.

## Bubble Up

> Summary to pass to parent during EXITING

- Module name mismatch: JS uses "ReplaceDialerModule", native returns "ForegroundBackgroundModule"
- toBackground() method is empty - needs implementation
- Methods are fire-and-forget (no callbacks/promises)
- Standard @ReactMethod pattern used

---

*Phase: EXITING | Depth: 2 | Parent: native-bridge*
