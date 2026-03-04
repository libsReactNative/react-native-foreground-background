# Understanding: Project Root

> Entry point for recursive understanding. Children are top-level logical domains.

## Project Overview

React Native Android library for managing foreground/background app switching. 
Provides native module bridge to bring app to foreground (wake up) and send to background.
Main use case: incoming call handling, dialer apps, telecom applications.

Version: 0.0.2, supports React Native 0.40.0+ (AndroidX compatible)

## Identified Domains

> Logical domains discovered. Each becomes a child directory for deeper exploration.

| Domain | Hypothesis | Priority | Status |
|--------|------------|----------|--------|
| native-bridge | React Native bridge connecting JS to Android native modules | HIGH | PENDING |
| foreground-management | Power management and screen wake logic for bringing app to front | HIGH | PENDING |
| package-structure | React package registration and module export | MEDIUM | PENDING |

## Source Mapping

> Which source paths map to which logical domains

| Source Path | -> Domain |
|-------------|----------|
| src/ForegroundBackground.js | native-bridge |
| android/app/src/main/java/.../ForegroundBackgroundModule.java | foreground-management, native-bridge |
| android/app/src/main/java/.../ForegroundBackgroundModulePackage.java | package-structure |

## Cross-Cutting Concerns

> Things that span multiple domains (may become ADRs)

- PowerManager.WakeLock usage for screen wake
- HandlerThread for background processing
- Intent flags for activity launching

## Synthesis

> Updated after all children complete

### From Children

**native-bridge**:
- React Native bridge with critical bugs
- Module name mismatch: JS expects "ReplaceDialerModule", native returns "ForegroundBackgroundModule"
- toBackground() method is empty - no implementation
- TDD flow recommended for testing fixes

**foreground-management**:
- Incoming call use case confirmed
- Deprecated FULL_WAKE_LOCK API needs modernization
- EXTRA_DOCK_STATE_CAR suggests automotive integration
- Error handling needs improvement
- SDD flow recommended for documentation

**package-structure**:
- Standard React Native package structure
- Android-only, v0.0.2 (early development)
- Documentation incomplete
- Telecom/dialer focused

### Combined Understanding

**Project Summary:**
React Native Android library for foreground/background app switching, designed for incoming call scenarios.

**Critical Issues Found:**
1. **BUG**: Module name mismatch will cause runtime failure
2. **BUG**: toBackground() has no implementation
3. **DEPRECATED**: FULL_WAKE_LOCK should be modernized
4. **DOCUMENTATION**: README incomplete, inconsistent naming

**Strengths:**
- Standard React Native architecture
- Dynamic class resolution
- Worker thread for background operations
- Timeout-based WakeLock release

**Recommendations:**
1. Fix module name mismatch immediately
2. Implement toBackground() functionality
3. Modernize WakeLock usage
4. Complete documentation
5. Add TDD for bridge reliability

### Flow Recommendations

| Flow Type | Module | Priority | Rationale |
|-----------|--------|----------|-----------|
| TDD | native-bridge | CRITICAL | Test bugs before fixing |
| SDD | foreground-management | HIGH | Document internal logic |
| SDD | package-structure | MEDIUM | Document package structure |

## Children Spawned

```
[native-bridge, foreground-management, package-structure] - ALL COMPLETED
```

---

*Created by /legacy ENTERING phase*
