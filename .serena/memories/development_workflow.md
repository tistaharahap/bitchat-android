# BitChat Android - Development Workflow

## Daily Development Commands

### Quick Development Cycle
```bash
# Clean, build, and install debug version
./gradlew clean assembleDebug installDebug

# Just build and install (faster)
./gradlew assembleDebug installDebug
```

### Code Quality Checks
```bash
# Run lint checks
./gradlew lintDebug

# Run unit tests
./gradlew test

# Full quality check
./gradlew build lint test
```

### Release Preparation
```bash
# Build release APK
./gradlew assembleRelease

# Build app bundle for Play Store
./gradlew bundleRelease

# Clean release build
./gradlew clean assembleRelease
```

## Common Development Tasks

### New Feature Development
1. Create feature branch: `git checkout -b feature/new-feature`
2. Implement changes in appropriate packages
3. Add unit tests for new functionality
4. Run quality checks: `./gradlew build lint test`
5. Test on device: `./gradlew installDebug`
6. Commit and create PR

### Bug Fixes
1. Identify issue and create branch: `git checkout -b fix/issue-description`
2. Add failing test first (TDD approach)
3. Implement fix
4. Verify fix: `./gradlew test`
5. Run full build: `./gradlew build`
6. Test on device to ensure no regression

### Code Review Checklist
- [ ] Lint passes: `./gradlew lintDebug`
- [ ] Tests pass: `./gradlew test`
- [ ] Builds successfully: `./gradlew assembleDebug`
- [ ] iOS compatibility maintained (protocol changes)
- [ ] Battery optimization considerations
- [ ] Security implications reviewed
- [ ] Performance impact assessed

## CI/CD Integration
- **GitHub Actions**: Automated build on all branches
- **Build Command**: `./gradlew build` (includes lint and test)
- **JDK**: 17 (Temurin distribution)
- **Runner**: ubuntu-latest

## Debugging Tools
- **ADB Logcat**: `adb logcat | grep bitchat`
- **Android Studio**: Full debugging support
- **Bluetooth Scanner**: For mesh network debugging
- **Network Inspector**: For Nostr relay debugging