# BitChat Android - Code Style & Conventions

## Language Settings
- **Kotlin Code Style**: Official (kotlin.code.style=official)
- **Java Target**: Version 1.8
- **Package Structure**: com.bitchat.android.*

## Naming Conventions
- **Files**: PascalCase for classes (ChatViewModel.kt, MainActivity.kt)
- **Classes**: PascalCase (ChatViewModel, BluetoothMeshService)
- **Methods**: camelCase (sendMessage, joinChannel, updateReactiveStates)
- **Properties**: camelCase (connectedPeers, currentChannel, showSidebar)
- **Constants**: UPPER_SNAKE_CASE (implied from companion objects)
- **Packages**: lowercase.with.dots following Java conventions

## Code Organization
- **Package Structure**: Domain-based organization
  - `ui/`: UI components and ViewModels
  - `mesh/`: Bluetooth mesh networking
  - `crypto/`: Encryption services
  - `model/`: Data models and DTOs
  - `protocol/`: Binary protocol handling
  - `services/`: Background services
  - `util/`: Utility classes
  - `nostr/`: Nostr protocol implementation
  - `geohash/`: Location-based features

## Documentation Style
- **Class Comments**: KDoc with purpose description
- **Method Comments**: Brief purpose for public methods
- **iOS Compatibility**: Comments note iOS compatibility where relevant
- **Architecture Comments**: Document manager pattern and delegation

## Type Usage
- **Sealed Classes**: For status enums (DeliveryStatus)
- **Data Classes**: For DTOs and models (@Parcelize for Android)
- **Companion Objects**: For constants and static methods
- **Properties**: Prefer val over var, use lateinit for DI

## Android Conventions
- **AndroidViewModel**: Used for app context access
- **Compose**: Modern declarative UI
- **Coroutines**: For async operations
- **LiveData**: Not heavily used, prefer Compose state
- **Dependency Injection**: Manual injection via constructors