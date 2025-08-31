# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

BitChat Android is a secure, decentralized P2P messaging app using Bluetooth mesh networks. This is the Android port of the iOS bitchat app, maintaining 100% protocol compatibility for cross-platform communication.

**Key Technologies**: Kotlin, Jetpack Compose, Bluetooth LE, Noise Protocol encryption, Nostr integration

## Documentation

Documentation about this project is available below:

@include docs/README.md

## Essential Commands

### Development Workflow

```bash
# Standard development cycle
./gradlew assembleDebug installDebug

# Full quality check before commits
./gradlew build lint test

# Clean build when needed
./gradlew clean assembleDebug
```

### Testing

```bash
# Run unit tests
./gradlew test

# Run instrumented tests (requires device)
./gradlew connectedAndroidTest

# Run specific test
./gradlew test --tests "*.MainViewModelTest"
```

### Code Quality

```bash
# Lint with existing baseline
./gradlew lintDebug

# Build release version
./gradlew assembleRelease
```

## Architecture Overview

### MVVM with Manager Delegation Pattern

The app uses a **delegation architecture** where `ChatViewModel` coordinates specialized managers:

- **DataManager**: Message/channel persistence (`ui/DataManager.kt`)
- **MessageManager**: Message processing (`ui/MessageManager.kt`)
- **ChannelManager**: Channel operations (`ui/ChannelManager.kt`)
- **PrivateChatManager**: Private messaging (`ui/PrivateChatManager.kt`)
- **CommandProcessor**: IRC-style commands (`ui/CommandProcessor.kt`)
- **NotificationManager**: Android notifications (`ui/NotificationManager.kt`)

### Bluetooth Mesh Architecture

**BluetoothMeshService** (`mesh/BluetoothMeshService.kt`) coordinates:

- **PacketProcessor**: Binary protocol handling
- **SecurityManager**: Encryption/decryption
- **PeerManager**: Peer discovery and tracking
- **FragmentManager**: Message fragmentation
- **StoreForwardManager**: Offline message storage

### Encryption Stack

**Dual encryption system**:

- **EncryptionService** (`crypto/EncryptionService.kt`): BouncyCastle operations
- **NoiseEncryptionService** (`noise/NoiseEncryptionService.kt`): Noise Protocol for channels
- **BinaryProtocol** (`protocol/BinaryProtocol.kt`): iOS-compatible packet format

### State Flow

```
UI (Compose) ↔ ChatViewModel ↔ Managers ↔ BluetoothMeshService ↔ BLE Hardware
                     ↓
              MeshDelegateHandler (mesh events)
```

## Critical Compatibility Requirements

### iOS Protocol Compatibility

**Must maintain 100% binary protocol compatibility** with iOS bitchat:

- Packet format in `protocol/BinaryProtocol.kt`
- Encryption algorithms in `crypto/` and `noise/`
- Message routing and TTL handling
- Peer ID generation and management

### Key Files for Protocol Changes

- `protocol/BinaryProtocol.kt`: Core packet encoding/decoding
- `mesh/SecurityManager.kt`: Message encryption/authentication
- `model/BitchatMessage.kt`: Message data structure
- `model/RoutedPacket.kt`: Network packet structure

## Package Organization

### Core Packages

- `ui/`: Jetpack Compose UI and ViewModels (ChatViewModel is central)
- `mesh/`: Bluetooth networking layer (BluetoothMeshService + managers)
- `crypto/`: Encryption services (BouncyCastle, Noise Protocol)
- `protocol/`: Binary protocol implementation (iOS compatibility)
- `model/`: Data classes and DTOs (Parcelable for Android)

### Feature Packages

- `nostr/`: Nostr protocol for geohash channels
- `geohash/`: Location-based messaging
- `onboarding/`: App initialization flow
- `services/`: Background services and utilities
- `util/`: Common utilities and extensions

## Development Notes

### Architecture Patterns

- **Manager Pattern**: ChatViewModel delegates to specialized managers rather than handling everything directly
- **Service Delegation**: BluetoothMeshService delegates BLE operations to specialized managers
- **Reactive State**: Compose state flows from ViewModel to UI
- **Background Service**: Persistent BLE operations via foreground service

### Critical Dependencies

- **Nordic BLE Library**: Required for stable Bluetooth LE operations
- **BouncyCastle**: Core cryptographic operations
- **Noise Protocol**: southernstorm implementation for channel encryption
- **Jetpack Compose**: Modern Android UI framework

### Testing Strategy

- Unit tests focus on crypto operations, protocol handling, and business logic
- Instrumented tests for UI interactions and Bluetooth functionality
- Mock Bluetooth operations for reliable unit testing
- Real device testing required for BLE functionality

### Build Configuration

- Uses Version Catalog (`gradle/libs.versions.toml`) for dependency management
- Lint baseline at `app/lint-baseline.xml` tracks accepted lint issues
- ProGuard enabled for release builds with crypto library exclusions
- Battery optimization whitelisting for background service
