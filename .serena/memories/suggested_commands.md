# BitChat Android - Development Commands

## Build Commands
```bash
# Build debug version
./gradlew assembleDebug

# Build release version (requires signing)
./gradlew assembleRelease

# Build app bundle for Google Play
./gradlew bundleRelease

# Clean build
./gradlew clean

# Full build with all checks
./gradlew build
```

## Installation Commands
```bash
# Install debug APK on connected device
./gradlew installDebug

# Install release APK on connected device
./gradlew installRelease

# Uninstall from device
adb uninstall com.bitchat.droid
```

## Testing Commands
```bash
# Run unit tests
./gradlew test

# Run instrumented tests (requires device/emulator)
./gradlew connectedAndroidTest

# Run specific test class
./gradlew test --tests "*.MainViewModelTest"

# Test with coverage
./gradlew testDebugUnitTestCoverageReport
```

## Code Quality Commands
```bash
# Run lint checks
./gradlew lint

# Generate lint report
./gradlew lintDebug

# Lint with baseline (current config)
./gradlew lint --baseline app/lint-baseline.xml
```

## Gradle Tasks
```bash
# List all available tasks
./gradlew tasks

# List all tasks including hidden ones
./gradlew tasks --all

# Show dependencies
./gradlew dependencies
```

## Development Workflow
```bash
# Standard development cycle
./gradlew clean && ./gradlew assembleDebug && ./gradlew installDebug

# Full quality check
./gradlew clean && ./gradlew build && ./gradlew lint && ./gradlew test
```

## System Commands (macOS)
- **Java**: Requires JDK 17+ (Android Studio includes this)
- **ADB**: Android Debug Bridge for device interaction
- **Git**: Standard git commands for version control
- **Find**: Uses BSD find (different from GNU find)
- **Grep**: Uses BSD grep (different from GNU grep)

## Notes
- Project uses Gradle wrapper (./gradlew) - don't need global Gradle installation
- Android Studio automatically configures Java and Android SDK
- Lint baseline exists at app/lint-baseline.xml to track known issues