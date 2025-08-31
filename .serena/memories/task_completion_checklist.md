# BitChat Android - Task Completion Checklist

## Before Completing Any Task

### 1. Code Quality Checks
```bash
# Run lint to check code style and potential issues
./gradlew lintDebug

# Build project to ensure compilation
./gradlew assembleDebug
```

### 2. Testing Requirements
```bash
# Run unit tests
./gradlew test

# If UI changes, run instrumented tests (requires device)
./gradlew connectedAndroidTest
```

### 3. Git Workflow
```bash
# Check current status
git status

# Review changes before committing
git diff

# Stage and commit with descriptive message
git add .
git commit -m "feat: description of changes

🤖 Generated with Claude Code
Co-Authored-By: Claude <noreply@anthropic.com>"
```

## Quality Standards
- **Lint**: Must pass lint checks or have justified baseline updates
- **Build**: Must compile successfully (assembleDebug)
- **Tests**: Unit tests must pass, instrumented tests for UI changes
- **iOS Compatibility**: Changes must maintain protocol compatibility
- **Security**: No hardcoded secrets, proper encryption usage

## Common Issues to Check
- **Bluetooth Permissions**: Ensure proper permission handling
- **Background Service**: BluetoothMeshService lifecycle management
- **Encryption**: Proper key management and secure deletion
- **Memory Leaks**: Proper lifecycle management in ViewModels
- **Performance**: Battery optimization compliance

## Release Checklist (Additional)
- Update versionCode and versionName in app/build.gradle.kts
- Test on multiple Android versions (API 26+)
- Verify Bluetooth LE functionality on physical devices
- Check cross-platform compatibility with iOS bitchat
- Validate ProGuard/R8 obfuscation doesn't break functionality