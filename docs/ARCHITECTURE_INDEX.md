# BitChat Android - Architecture Documentation Index

> Comprehensive navigation documentation for the BitChat Android secure P2P messaging app

## 📋 Project Overview

BitChat Android is a secure, decentralized P2P messaging app featuring:
- **Bluetooth LE mesh networking** with multi-hop relay
- **Noise Protocol encryption** (X25519 + AES-256-GCM) 
- **100% iOS protocol compatibility** for cross-platform communication
- **Nostr integration** for geohash-based location channels
- **Modern Android architecture** with Jetpack Compose + MVVM

**Version**: 1.2.2 (versionCode 18)  
**Target Android**: API 34 (Android 14)  
**Minimum Android**: API 26 (Android 8.0)  
**Package**: `com.bitchat.droid`

---

## 🏗️ System Architecture Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        UI Layer                             │
├─────────────────────────────────────────────────────────────┤
│         ViewModel Layer (MVVM Pattern)                      │
├─────────────────────────────────────────────────────────────┤
│                   Service Layer                             │
├─────────────────────────────────────────────────────────────┤
│               Protocol & Crypto Layer                       │
├─────────────────────────────────────────────────────────────┤
│                 Platform Layer                              │
└─────────────────────────────────────────────────────────────┘
```

### Component Relationship Diagram

```
BitchatApplication
    ├── MainActivity (UI Host)
    │   ├── MainViewModel (Onboarding State)
    │   └── ChatViewModel (App State)
    │       ├── BluetoothMeshService (Mesh Coordinator)
    │       │   ├── BluetoothConnectionManager (BLE)
    │       │   ├── PeerManager (Peer Lifecycle)
    │       │   ├── SecurityManager (Encryption)
    │       │   ├── MessageHandler (Protocol)
    │       │   └── FragmentManager (Large Messages)
    │       ├── NostrGeohashService (Location/Nostr)
    │       └── EncryptionService (Noise Protocol)
    └── Component Managers
        ├── DataManager (State Persistence)
        ├── ChannelManager (Channel Logic)
        ├── PrivateChatManager (DM Logic)
        └── NotificationManager (System Notifications)
```

---

## 📁 Directory Structure & Navigation

### Root Application Files
| File | Purpose | Key Classes |
|------|---------|-------------|
| [`BitchatApplication.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/BitchatApplication.kt) | App initialization | TorManager, RelayDirectory init |
| [`MainActivity.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/MainActivity.kt) | Main UI host, permission flow | OnboardingCoordinator, permission managers |
| [`MainViewModel.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/MainViewModel.kt) | Onboarding state management | OnboardingState, status flows |

### Core Architecture Packages

#### `/mesh` - Bluetooth Mesh Networking Layer
**Central Service**: [`BluetoothMeshService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt)
- **Purpose**: Mesh network coordinator with component-based architecture
- **Key Methods**: `startServices()`, `sendMessage()`, `sendPrivateMessage()`
- **Components Integration**: Coordinates all mesh managers

**Connection Management**:
- [`BluetoothConnectionManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothConnectionManager.kt) - BLE GATT client/server
- [`BluetoothGattClientManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothGattClientManager.kt) - BLE client operations
- [`BluetoothGattServerManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothGattServerManager.kt) - BLE server operations
- [`BluetoothConnectionTracker.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothConnectionTracker.kt) - Connection state tracking

**Peer & Message Management**:
- [`PeerManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PeerManager.kt) - Peer lifecycle, verification, fingerprints
- [`MessageHandler.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt) - Message type processing
- [`PacketProcessor.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt) - Incoming packet routing
- [`FragmentManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt) - Large message fragmentation
- [`PacketRelayManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt) - Multi-hop message relay

**Security & Storage**:
- [`SecurityManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt) - Packet validation, Noise handshakes
- [`StoreForwardManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/StoreForwardManager.kt) - Offline message caching
- [`PeerFingerprintManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PeerFingerprintManager.kt) - Centralized fingerprint tracking

**System Management**:
- [`BluetoothPermissionManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothPermissionManager.kt) - BLE permission handling
- [`PowerManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PowerManager.kt) - Battery optimization
- [`BluetoothPacketBroadcaster.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothPacketBroadcaster.kt) - Packet broadcasting

#### `/crypto` - Encryption Architecture
**Main Service**: [`EncryptionService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/crypto/EncryptionService.kt)
- **Purpose**: Unified encryption interface using Noise Protocol
- **Key Features**: Session management, Ed25519 signatures, channel encryption
- **Dependencies**: NoiseEncryptionService, BouncyCastle

#### `/noise` - Noise Protocol Implementation
**Core Service**: [`NoiseEncryptionService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseEncryptionService.kt)
- **Purpose**: Noise protocol transport encryption with session management
- **Key Features**: Static identity, handshake management, channel encryption
- **Related Classes**: 
  - [`NoiseSession.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseSession.kt) - Individual session state
  - [`NoiseSessionManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseSessionManager.kt) - Session lifecycle
  - [`NoiseChannelEncryption.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseChannelEncryption.kt) - Channel password encryption

#### `/protocol` - Binary Protocol & Compatibility
**Binary Protocol**: [`BinaryProtocol.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt)
- **Purpose**: 100% iOS-compatible binary packet format
- **Features**: 13-byte header, compression, fragmentation, signatures
- **Related**: 
  - [`CompressionUtil.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/protocol/CompressionUtil.kt) - LZ4 compression
  - [`MessagePadding.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/protocol/MessagePadding.kt) - Traffic analysis resistance

#### `/model` - Data Models
**Core Message Model**: [`BitchatMessage.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/model/BitchatMessage.kt)
- **Purpose**: Primary message data structure with iOS compatibility
- **Features**: Binary serialization, delivery status, mentions, channels

**Protocol Models**:
- [`RoutedPacket.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/model/RoutedPacket.kt) - Packet routing metadata
- [`IdentityAnnouncement.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/model/IdentityAnnouncement.kt) - Peer identity broadcast
- [`NoiseEncrypted.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/model/NoiseEncrypted.kt) - Encrypted payload container
- [`FragmentPayload.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/model/FragmentPayload.kt) - Message fragmentation

#### `/nostr` - Nostr Protocol Integration
**Main Service**: [`NostrGeohashService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrGeohashService.kt)
- **Purpose**: Nostr relay integration for geohash channels and DMs
- **Key Features**: Ephemeral events, gift wrap DMs, participant tracking

**Core Components**:
- [`NostrRelayManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt) - WebSocket relay connections
- [`NostrTransport.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrTransport.kt) - Message routing via Nostr
- [`NostrIdentity.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt) - Identity management
- [`NostrProtocol.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrProtocol.kt) - NIP-17 gift wrap protocol

**Supporting Classes**:
- [`NostrEvent.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt) - Nostr event structure
- [`NostrProofOfWork.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrProofOfWork.kt) - PoW mining (NIP-13)
- [`NostrCrypto.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt) - Secp256k1 operations

#### `/ui` - User Interface Layer
**Main ViewModel**: [`ChatViewModel.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt)
- **Purpose**: MVVM coordinator for app state and business logic
- **Architecture**: Delegates to specialized managers
- **Dependencies**: BluetoothMeshService, NostrGeohashService

**UI Managers**:
- [`DataManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/DataManager.kt) - State persistence
- [`MessageManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/MessageManager.kt) - Message timeline
- [`ChannelManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/ChannelManager.kt) - Channel operations
- [`PrivateChatManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/PrivateChatManager.kt) - Private messaging
- [`NotificationManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/NotificationManager.kt) - System notifications

**UI Components**:
- [`ChatScreen.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/ChatScreen.kt) - Main chat interface
- [`MessageComponents.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/MessageComponents.kt) - Message rendering
- [`InputComponents.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/InputComponents.kt) - Input handling
- [`SidebarComponents.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/SidebarComponents.kt) - Navigation sidebar

---

## 🔑 Core API Reference

### BluetoothMeshService API

```kotlin
class BluetoothMeshService(context: Context) {
    // Lifecycle
    fun startServices(): Boolean
    fun stopServices()
    
    // Messaging
    fun sendMessage(content: String, mentions: List<String>, channel: String?)
    fun sendPrivateMessage(content: String, recipientPeerID: String, recipientNickname: String, messageID: String?)
    fun sendReadReceipt(messageID: String, recipientPeerID: String, readerNickname: String)
    
    // Peer Management
    fun getPeerNicknames(): Map<String, String>
    fun getPeerInfo(peerID: String): PeerInfo?
    fun hasEstablishedSession(peerID: String): Boolean
    fun getSessionState(peerID: String): NoiseSessionState
    
    // Identity & Security
    val myPeerID: String
    fun getIdentityFingerprint(): String
    fun shouldShowEncryptionIcon(peerID: String): Boolean
    
    // Debug & Diagnostics
    fun getDebugStatus(): String
    fun clearAllInternalData()
}
```

### EncryptionService API

```kotlin
class EncryptionService(context: Context) {
    // Noise Protocol
    fun hasEstablishedSession(peerID: String): Boolean
    fun initiateHandshake(peerID: String): ByteArray?
    fun processHandshakeMessage(data: ByteArray, peerID: String): ByteArray?
    fun encrypt(data: ByteArray, peerID: String): ByteArray
    fun decrypt(data: ByteArray, peerID: String): ByteArray
    
    // Channel Encryption
    fun setChannelPassword(password: String, channel: String)
    fun encryptChannelMessage(message: String, channel: String): ByteArray?
    fun decryptChannelMessage(encryptedData: ByteArray, channel: String): String?
    
    // Identity & Signatures
    fun getStaticPublicKey(): ByteArray?
    fun getSigningPublicKey(): ByteArray?
    fun signData(data: ByteArray): ByteArray?
    fun verifyEd25519Signature(signature: ByteArray, data: ByteArray, publicKey: ByteArray): Boolean
    fun getIdentityFingerprint(): String
}
```

### ChatViewModel API

```kotlin
class ChatViewModel(application: Application, meshService: BluetoothMeshService) : AndroidViewModel(application), BluetoothMeshDelegate {
    // Core messaging
    fun sendMessage(content: String)
    fun setNickname(newNickname: String)
    
    // Channel management
    fun joinChannel(channel: String, password: String?): Boolean
    fun switchToChannel(channel: String?)
    fun leaveChannel(channel: String)
    
    // Private messaging
    fun startPrivateChat(peerID: String)
    fun endPrivateChat()
    fun toggleFavorite(peerID: String)
    
    // Navigation
    fun handleBackPressed(): Boolean
    fun showSidebar() / hideSidebar()
    fun showAppInfo() / hideAppInfo()
    
    // Geohash integration
    fun selectLocationChannel(channel: ChannelID)
    fun geohashParticipantCount(geohash: String): Int
    fun startGeohashDM(pubkeyHex: String)
    
    // Emergency
    fun panicClearAllData()
}
```

---

## 🌐 Protocol Compatibility

### iOS Compatibility Matrix

| Component | Compatibility | Notes |
|-----------|---------------|-------|
| **Binary Protocol** | 100% | Identical 13-byte header, packet types, UUIDs |
| **Noise Encryption** | 100% | X25519 + AES-256-GCM, same handshake flow |
| **Message Format** | 100% | TLV encoding, fragmentation, compression |
| **Peer IDs** | 100% | 8-byte (16-hex) format, rotation support |
| **Bluetooth UUIDs** | 100% | Identical service/characteristic identifiers |
| **Channel Protocol** | 100% | Commands, password protection, ownership |

### Cross-Platform Integration Points

**Message Exchange**:
- **Public Messages**: Direct mesh broadcast compatibility
- **Private Messages**: Noise-encrypted with delivery/read receipts
- **Channel Messages**: Password-derived encryption compatibility
- **Fragmentation**: Large message splitting (>244 bytes)

**Peer Discovery**:
- **Identity Announcements**: TLV-encoded with public keys
- **Fingerprint System**: SHA-256 hashes for persistent identity
- **Session Management**: Compatible Noise handshake flow

---

## 📡 Mesh Networking Architecture

### Bluetooth LE Mesh Design

```
Device Role: Central + Peripheral (Simultaneous)
┌──────────────────────────────────────────────────┐
│                 BLE Stack                        │
├──────────────────────────────────────────────────┤
│  BluetoothGattServerManager (Peripheral Role)    │
│  - Service UUID: F0E1D2C3-B4A5-9687-8796-5A4B3C2D1E0F
│  - Characteristic: TX (notify), RX (write)       │
├──────────────────────────────────────────────────┤
│  BluetoothGattClientManager (Central Role)       │
│  - Scanning for peripherals                     │
│  - Connection management                         │
├──────────────────────────────────────────────────┤
│  BluetoothConnectionManager (Coordinator)        │
│  - Device discovery & connection                 │
│  - Packet routing & relay                       │
└──────────────────────────────────────────────────┘
```

### Packet Flow

```
Incoming Packet → PacketProcessor → Validation → Routing
    ├── ANNOUNCE → MessageHandler → PeerManager
    ├── MESSAGE → MessageHandler → UI/Channels
    ├── NOISE_HANDSHAKE → SecurityManager → Session
    ├── NOISE_ENCRYPTED → MessageHandler → Decrypt → UI
    ├── FRAGMENT → FragmentManager → Reassemble
    └── LEAVE → MessageHandler → PeerManager
```

---

## 🔐 Encryption Architecture

### Noise Protocol Flow

```
Session States:
┌─────────────┐    handshake    ┌─────────────┐    established    ┌─────────────┐
│  not_init   │ ──────────────→ │ handshaking │ ──────────────→   │ established │
└─────────────┘                 └─────────────┘                   └─────────────┘
                                        ↓ failure
                                ┌─────────────┐
                                │   failed    │
                                └─────────────┘
```

### Cryptographic Components

**Key Management**:
- **Static Identity**: X25519 key pair (persistent across sessions)
- **Signing Keys**: Ed25519 key pair (packet authentication)
- **Session Keys**: Ephemeral per-peer (forward secrecy)
- **Channel Keys**: Argon2id password derivation

**Security Layers**:
1. **Transport**: Noise Protocol (X25519 + AES-256-GCM)
2. **Authentication**: Ed25519 signatures on packets
3. **Channel Security**: Password-based group encryption
4. **Traffic Analysis**: Padding, dummy messages, timing obfuscation

---

## 🌍 Nostr Integration Architecture

### Geohash Channel System

```
Location Precision Levels:
┌─────────────┬─────────────┬──────────────┐
│    Level    │   Length    │   Coverage   │
├─────────────┼─────────────┼──────────────┤
│   REGION    │      2      │   ~1200km    │
│  PROVINCE   │      4      │    ~75km     │
│    CITY     │      5      │    ~4.8km    │
│NEIGHBORHOOD │      6      │    ~1.2km    │
│   BLOCK     │      7      │    ~150m     │
└─────────────┴─────────────┴──────────────┘
```

### Nostr Message Types

**Kind 20000** (Ephemeral): Geohash channel messages
- **Tags**: `g` (geohash), `n` (nickname), `t` (teleport)
- **Content**: Plain text message
- **TTL**: 1 hour (relay-dependent)

**Kind 1059** (Gift Wrap): Private messages via NIP-17
- **Encryption**: X25519 + AES-256-GCM
- **Content**: BitChat packet embedding
- **Relay Strategy**: Default relays for max reach

---

## 🎨 UI Architecture

### Jetpack Compose Design

**Theme System**: [`Theme.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/theme/Theme.kt)
- **Design**: Material Design 3 with terminal aesthetic
- **Modes**: Light/Dark theme matching iOS
- **Colors**: Consistent peer colors via deterministic hashing

**Component Architecture**:
- **State Management**: ChatState + LiveData reactive updates
- **Composition**: Reusable components with clear boundaries
- **Navigation**: Back-press handling, modal management

### Key UI Components

**Message Rendering**:
- [`MessageComponents.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/MessageComponents.kt) - Message bubbles, timestamps, status
- [`LinkPreviewPill.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/LinkPreviewPill.kt) - URL previews
- [`PoWStatusIndicator.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/PoWStatusIndicator.kt) - Mining status
- [`MatrixEncryptionAnimation.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/MatrixEncryptionAnimation.kt) - PoW visual feedback

**Input & Commands**:
- [`CommandProcessor.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/CommandProcessor.kt) - IRC-style commands
- [`MessageSpecialParser.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/MessageSpecialParser.kt) - Special content parsing

---

## 📋 Integration Patterns

### Dependency Injection Pattern

```kotlin
// Application Level
BitchatApplication → Initialize core services (Tor, RelayDirectory)

// Activity Level  
MainActivity → Create BluetoothMeshService → Inject into ChatViewModel

// ViewModel Level
ChatViewModel → Coordinate managers → Delegate to specialized services
```

### Delegation Pattern

**BluetoothMeshService** coordinates via delegates:
- `PeerManagerDelegate` → Peer lifecycle notifications
- `SecurityManagerDelegate` → Key exchange callbacks
- `MessageHandlerDelegate` → Message processing hooks
- `BluetoothConnectionManagerDelegate` → Connection events

### Observer Pattern

**State Management**:
- `StateFlow`/`LiveData` for reactive UI updates
- Location channel observers for geohash switching
- Session state monitoring for encryption status

---

## 🔧 Development Guidelines

### File Organization Standards

```
com.bitchat.android/
├── Core application (3 files)
├── mesh/ (16 files) - Bluetooth mesh networking
├── crypto/ (1 file) - Encryption service facade  
├── noise/ (4 files + southernstorm/) - Noise protocol
├── protocol/ (3 files) - Binary protocol & compression
├── model/ (5 files) - Data structures
├── nostr/ (19 files) - Nostr protocol integration
├── ui/ (21 files) - User interface & managers
├── services/ (4 files) - Background services
├── geohash/ (3 files) - Location channel system
├── onboarding/ (12 files) - Permission flow
├── identity/ (1 file) - Secure identity storage
├── favorites/ (1 file) - Favorites persistence
├── net/ (4 files) - Tor integration
└── util/ (2 files) - Utility functions
```

### Architectural Principles

**Separation of Concerns**: Each package handles distinct functionality
- `mesh/` → Bluetooth networking & peer management
- `crypto/` → Encryption & cryptographic operations  
- `ui/` → User interface & presentation logic
- `protocol/` → Wire format & iOS compatibility

**Dependency Flow**: UI → Services → Protocol → Platform
- UI depends on services, not protocol details
- Services coordinate between protocol layers
- Clear abstraction boundaries

**iOS Compatibility**: Maintain protocol-level compatibility
- Binary packet format identical
- Cryptographic algorithms consistent
- Message types and routing logic preserved

---

## 🧪 Testing Architecture

### Test Organization

```
app/src/test/kotlin/com/bitchat/
├── NotificationManagerTest.kt - Notification logic testing
├── ColorTest.kt - Color consistency testing  
├── MainViewModelTest.kt - Onboarding flow testing
└── PeerManagerTest.kt - Peer lifecycle testing
```

### Testing Strategies

**Unit Tests**: Individual component testing
- **PeerManager**: Peer lifecycle, fingerprint management
- **NotificationManager**: Notification logic, timing
- **MainViewModel**: Onboarding state machine

**Integration Testing**: Cross-component interaction
- **Mesh Service**: End-to-end message flow
- **Encryption**: Noise protocol handshake
- **UI Components**: User interaction flows

---

## 🔍 Debugging & Diagnostics

### Debug Information Access

```kotlin
// Mesh service debug
val debugStatus = meshService.getDebugStatus()

// Component-specific debug
val peerDebug = peerManager.getDebugInfo()
val connectionDebug = connectionManager.getDebugInfo()
val encryptionDebug = encryptionService.getDebugInfo()
```

### Logging Categories

**BluetoothMeshService**: Core service lifecycle, message sending
**PeerManager**: Peer discovery, nickname updates, fingerprints
**SecurityManager**: Handshakes, session establishment, validation
**NostrGeohashService**: Location channels, participant tracking
**EncryptionService**: Session states, key operations

### Performance Monitoring

**Connection Metrics**: RSSI values, connection counts, device addresses
**Message Metrics**: Send/receive counts, encryption status, relay hops
**Session Metrics**: Established sessions, handshake success rate
**Battery Metrics**: Power mode states, scanning duty cycles

---

## 🚀 Deployment & Configuration

### Build Configuration

**Gradle Files**:
- [`app/build.gradle.kts`](/Users/tista/codes/bitchat-android/app/build.gradle.kts) - App module configuration
- [`build.gradle.kts`](/Users/tista/codes/bitchat-android/build.gradle.kts) - Project-level configuration
- [`settings.gradle.kts`](/Users/tista/codes/bitchat-android/settings.gradle.kts) - Module settings

**Key Dependencies**:
- **Jetpack Compose**: UI framework
- **BouncyCastle**: Cryptographic operations
- **Nordic BLE**: Reliable Bluetooth LE
- **Arti-Mobile**: Tor integration
- **kotlinx-coroutines**: Async operations

### Release Checklist

1. **Version Update**: Increment `versionCode` and `versionName` in build.gradle.kts
2. **Security Review**: Verify encryption implementations
3. **iOS Compatibility**: Test cross-platform communication
4. **Performance Testing**: Battery usage, connection reliability
5. **Permission Validation**: Verify onboarding flow completeness

---

## 📚 Related Documentation

- [**README.md**](/Users/tista/codes/bitchat-android/README.md) - Project overview and setup
- [**CHANGELOG.md**](/Users/tista/codes/bitchat-android/CHANGELOG.md) - Version history  
- [**PRIVACY_POLICY.md**](/Users/tista/codes/bitchat-android/PRIVACY_POLICY.md) - Privacy practices
- [**LICENSE.md**](/Users/tista/codes/bitchat-android/LICENSE.md) - Public domain license

### External References

- **iOS BitChat**: [github.com/jackjackbits/bitchat](https://github.com/jackjackbits/bitchat)
- **Noise Protocol**: [noiseprotocol.org](https://noiseprotocol.org)
- **Nostr Protocol**: [github.com/nostr-protocol/nips](https://github.com/nostr-protocol/nips)
- **Android BLE Guide**: [developer.android.com/guide/topics/connectivity/bluetooth](https://developer.android.com/guide/topics/connectivity/bluetooth)

---

*Generated on 2025-08-31 - BitChat Android v1.2.2*