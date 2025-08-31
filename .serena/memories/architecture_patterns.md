# BitChat Android - Architecture & Design Patterns

## Overall Architecture
- **Pattern**: MVVM (Model-View-ViewModel) with Jetpack Compose
- **State Management**: ChatViewModel as central coordinator
- **Delegation Pattern**: Specialized managers for domain concerns
- **Service Architecture**: BluetoothMeshService for background operations

## Key Architectural Components

### Core Managers (Delegation Pattern)
- **DataManager**: Message and channel data management
- **MessageManager**: Message processing and display logic
- **ChannelManager**: Channel operations and state
- **PrivateChatManager**: Private messaging functionality
- **CommandProcessor**: IRC-style command handling
- **NotificationManager**: Android notification system
- **MeshDelegateHandler**: Bluetooth mesh event handling

### Mesh Networking Layer
- **BluetoothMeshService**: Background service for BLE operations
- **PacketProcessor**: Binary protocol handling
- **SecurityManager**: Encryption and key management
- **PeerManager**: Peer discovery and connection tracking
- **FragmentManager**: Message fragmentation/reassembly
- **StoreForwardManager**: Offline message storage

### Encryption Architecture
- **EncryptionService**: Primary crypto operations (BouncyCastle)
- **NoiseEncryptionService**: Noise Protocol implementation
- **NoiseSessionManager**: Session key management
- **SecureIdentityStateManager**: Identity persistence

### Protocol Compatibility
- **BinaryProtocol**: 100% iOS-compatible binary packet format
- **MessagePadding**: Traffic analysis resistance
- **CompressionUtil**: LZ4 message compression

## Design Principles
- **iOS Compatibility**: Maintain 100% protocol compatibility
- **Security First**: Encryption by default, secure storage
- **Battery Optimization**: Adaptive power management
- **Modular Design**: Clear separation of concerns
- **Manager Pattern**: Delegate complex operations to specialized managers
- **Reactive UI**: Compose state management with reactive updates

## State Management
- **ChatState**: UI state container
- **LiveData**: Minimal usage, prefer Compose state
- **ViewModel Scope**: Coroutines tied to ViewModel lifecycle
- **Shared Preferences**: Encrypted preferences for persistence

## Threading Model
- **Main Thread**: UI operations and Compose
- **Background Service**: BluetoothMeshService on dedicated thread
- **Coroutines**: Async operations with proper scope management
- **ViewModelScope**: ViewModel-bound coroutines for UI logic