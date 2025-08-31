# BitChat Android - Project Structure

## Root Directory
```
bitchat-android/
├── app/                    # Main application module
├── .github/               # GitHub workflows and templates
├── gradle/                # Gradle wrapper and libs.versions.toml
├── fastlane/             # App store deployment (metadata only)
├── .serena/              # Serena MCP configuration
├── build.gradle.kts      # Top-level build configuration
├── settings.gradle.kts   # Gradle settings
├── gradle.properties     # Project-wide Gradle properties
├── gradlew               # Gradle wrapper (Unix)
├── gradlew.bat          # Gradle wrapper (Windows)
└── README.md            # Project documentation
```

## App Module Structure
```
app/
├── src/
│   ├── main/
│   │   ├── java/com/bitchat/android/
│   │   │   ├── MainActivity.kt           # Main activity
│   │   │   ├── BitchatApplication.kt     # Application class
│   │   │   ├── MainViewModel.kt          # App-level ViewModel
│   │   │   ├── ui/                       # UI layer
│   │   │   │   ├── ChatViewModel.kt      # Main chat logic
│   │   │   │   ├── ChatScreen.kt         # Primary UI screen
│   │   │   │   ├── theme/                # Material Design 3 theming
│   │   │   │   └── [various UI components]
│   │   │   ├── mesh/                     # Bluetooth mesh networking
│   │   │   │   ├── BluetoothMeshService.kt
│   │   │   │   └── [networking managers]
│   │   │   ├── crypto/                   # Encryption services
│   │   │   ├── protocol/                 # Binary protocol handling
│   │   │   ├── model/                    # Data models
│   │   │   ├── services/                 # Background services
│   │   │   ├── nostr/                    # Nostr protocol implementation
│   │   │   ├── geohash/                  # Location-based features
│   │   │   ├── noise/                    # Noise Protocol crypto
│   │   │   ├── util/                     # Utility classes
│   │   │   ├── identity/                 # Identity management
│   │   │   ├── favorites/                # Favorites persistence
│   │   │   ├── onboarding/              # App onboarding flow
│   │   │   ├── net/                     # Network utilities (Tor)
│   │   │   └── core/                    # Core utilities
│   │   ├── res/                         # Android resources
│   │   ├── assets/                      # Asset files (nostr_relays.csv)
│   │   └── AndroidManifest.xml          # App manifest
│   └── test/                            # Unit tests
├── build.gradle.kts                     # App module build config
├── proguard-rules.pro                   # ProGuard configuration
└── lint-baseline.xml                    # Lint baseline
```

## Package Organization (Domain-Driven)
- **ui/**: User interface and ViewModels
- **mesh/**: Bluetooth mesh networking layer
- **crypto/**: Cryptographic operations
- **protocol/**: Binary protocol implementation
- **model/**: Data classes and DTOs
- **services/**: Background services and utilities
- **nostr/**: Nostr protocol for geohash channels
- **util/**: Common utilities and extensions
- **onboarding/**: App initialization and permission flow