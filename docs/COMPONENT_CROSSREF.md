# BitChat Android - Component Cross-Reference

> Navigation guide showing relationships and integration patterns between components

## 🔗 Service Interaction Matrix

### Primary Service Dependencies

```
ChatViewModel
├── BluetoothMeshService (mesh networking)
│   ├── PeerManager (peer lifecycle)
│   ├── SecurityManager (encryption coordination)
│   ├── MessageHandler (message processing)
│   ├── BluetoothConnectionManager (BLE operations)
│   ├── FragmentManager (large messages)
│   └── PacketRelayManager (multi-hop routing)
├── NostrGeohashService (location channels)
│   ├── NostrRelayManager (relay connections)
│   ├── NostrTransport (message routing)
│   └── LocationChannelManager (geohash computation)
└── EncryptionService (crypto facade)
    └── NoiseEncryptionService (noise protocol)
```

### Manager Coordination Patterns

```
DataManager ←→ All UI Managers (state persistence)
NotificationManager ←→ All Services (system notifications)
MessageManager ←→ All Message Sources (timeline coordination)
PrivateChatManager ←→ All DM Sources (private message handling)
```

---

## 🔄 Data Flow Patterns

### Incoming Message Flow

```
BLE Packet → BluetoothConnectionManager
    ↓
PacketProcessor (validation, routing)
    ↓
MessageHandler (type-specific processing)
    ├── ANNOUNCE → PeerManager (peer discovery)
    ├── MESSAGE → MessageManager (public messages)
    ├── NOISE_HANDSHAKE → SecurityManager (handshake)
    ├── NOISE_ENCRYPTED → EncryptionService → Decrypt
    │                     ↓
    │                   NoisePayload Router
    │                     ├── PRIVATE_MESSAGE → PrivateChatManager
    │                     ├── DELIVERED → DeliveryStatus Update
    │                     └── READ_RECEIPT → ReadStatus Update
    └── FRAGMENT → FragmentManager (reassembly)
```

### Outgoing Message Flow

```
User Input → ChatViewModel
    ↓
Message Type Detection
    ├── Public Message → BluetoothMeshService.sendMessage()
    │                      ↓
    │                   BinaryProtocol.encode() → BLE Broadcast
    ├── Private Message → BluetoothMeshService.sendPrivateMessage()
    │                       ↓
    │                    EncryptionService.encrypt() → Noise Session
    │                       ↓
    │                    BinaryProtocol.encode() → BLE Direct Send
    └── Channel Message → ChannelManager.encryptChannelMessage()
                            ↓
                         Password-based encryption → BLE Broadcast
```

### Geohash Message Flow

```
User Input → ChatViewModel
    ↓
NostrGeohashService.sendGeohashMessage()
    ↓
NostrIdentityBridge.deriveIdentity() (geohash-specific)
    ↓
NostrProtocol.createEphemeralGeohashEvent() (kind 20000)
    ↓
NostrRelayManager.sendEventToGeohash() (geohash-specific relays)

Incoming:
Nostr Relay → NostrRelayManager (WebSocket)
    ↓
NostrGeohashService.handleUnifiedGeohashEvent()
    ├── Participant Tracking (all geohashes)
    ├── Nickname Caching (all geohashes)
    ├── Message Display (current geohash only)
    └── Notification Triggers (mentions, first messages)
```

---

## 🎯 Component Integration Points

### Encryption Integration

#### NoiseEncryptionService ↔ BluetoothMeshService
```kotlin
// Handshake coordination
meshService.securityManager.handleHandshakePacket() 
    → encryptionService.processHandshakeMessage()
    → sessionEstablished callback
    → peerManager.storeFingerprintForPeer()

// Message encryption
meshService.sendPrivateMessage() 
    → encryptionService.encrypt()
    → NoiseSession.encrypt()
    → packetBroadcaster.sendToSpecificPeer()
```

#### Channel Encryption Integration
```kotlin
// Password-based encryption
channelManager.setChannelPassword() 
    → encryptionService.setChannelPassword()
    → NoiseChannelEncryption.setChannelPassword()
    → Argon2id key derivation

// Message encryption/decryption
messageHandler.processChannelMessage()
    → encryptionService.decryptChannelMessage()
    → AES-256-GCM decryption
```

### UI State Integration

#### ChatState ↔ Service Integration
```kotlin
// Reactive state updates
BluetoothMeshService.onPeerConnected 
    → ChatState.setConnectedPeers()
    → UI recomposition

NostrGeohashService.updateGeohashParticipant()
    → ChatState.setGeohashParticipantCounts()
    → LocationChannelSelector UI update

PeerManager.updatePeerNickname()
    → ChatState.setPeerNicknames()
    → MessageComponent sender label update
```

#### Navigation State Flow
```kotlin
// Back navigation handling
ChatScreen.BackHandler
    → ChatViewModel.handleBackPressed()
    → State transition logic
    → UI navigation updates

// Modal management
ChatState.showPasswordPrompt
    → PasswordPromptDialog visibility
    → ChannelManager.joinChannel() completion
    → ChatState.setCurrentChannel()
```

### Notification Integration

#### Cross-Service Notification Triggers
```kotlin
// Mesh mention notifications
MessageManager.addMessage() (with mentions)
    → NotificationManager.showMeshMentionNotification()
    → System notification display

// Geohash notifications
NostrGeohashService.handleUnifiedGeohashEvent()
    → checkAndTriggerGeohashNotifications()
    → NotificationManager.showGeohashNotification()
    → System notification with location context

// Private message notifications
PrivateChatManager.handleIncomingPrivateMessage()
    → NotificationManager.showPrivateMessageNotification()
    → System notification with chat context
```

---

## 🔐 Security Integration Patterns

### Trust Establishment Flow

```
Peer Discovery → Identity Announcement
    ↓
PeerManager.addPeer() → SecurityManager.validateIncomingPacket()
    ↓
Noise Handshake Initiation → NoiseEncryptionService.initiateHandshake()
    ↓
Handshake Completion → PeerFingerprintManager.storeFingerprintForPeer()
    ↓
Session Established → BluetoothMeshService.onSessionEstablished()
    ↓
Encrypted Communication Ready
```

### Message Validation Pipeline

```
Incoming Packet → BLE Reception
    ↓
BinaryProtocol.decode() (packet validation)
    ↓
SecurityManager.validateIncomingPacket() (signature, rate limits)
    ↓
MessageHandler.processMessage() (type-specific validation)
    ↓
EncryptionService.decrypt() (if encrypted)
    ↓
Content Filtering → UI Display
```

---

## 🌐 Nostr Integration Patterns

### Identity Management Integration

```
Geohash Channel Selection → LocationChannelManager.select()
    ↓
NostrIdentityBridge.deriveIdentity() (geohash-specific identity)
    ↓
NostrRelayManager.subscribe() (subscription setup)
    ↓
NostrGeohashService.switchLocationChannel() (async subscription)
```

### Cross-Protocol Message Mapping

```
Mesh Peer ↔ Nostr Integration:

Favorites System:
FavoritesPersistenceService
    ├── NoiseKey → NostrPubkey mapping
    ├── Cross-platform favorite sync
    └── Identity persistence across protocols

Message Unification:
ConversationAliasResolver
    ├── Mesh PeerID ↔ Noise Key ↔ Nostr Pubkey
    ├── Chat history unification  
    └── Identity resolution across restarts
```

---

## 📱 UI Component Relationships

### State Management Flow

```
User Action → Compose UI Component
    ↓
ChatViewModel Method Call
    ↓
Service Layer Operation
    ↓
ChatState Update (reactive)
    ↓
UI Recomposition (automatic)
```

### Component Hierarchy

```
ChatScreen (main UI host)
├── MessageTimeline
│   ├── MessageComponent (individual messages)
│   ├── SystemMessageComponent (commands, status)
│   └── TypingIndicator
├── MessageInput
│   ├── CommandSuggestions
│   ├── MentionSuggestions
│   └── InputActions
├── Sidebar
│   ├── ChannelList
│   ├── PrivateChatList
│   ├── PeerList
│   └── LocationChannelSelector
└── Modals
    ├── PasswordPrompt
    ├── AppInfoDialog
    └── SettingsSheet
```

---

## 🔄 Event Coordination Patterns

### Service Event Broadcasting

#### BluetoothMeshService Events
```kotlin
interface BluetoothMeshDelegate {
    // Peer events
    fun onPeerConnected(peerID: String, peerInfo: PeerInfo)
    fun onPeerDisconnected(peerID: String)
    fun onPeerNicknameUpdated(peerID: String, nickname: String)
    
    // Message events
    fun didReceiveMessage(message: BitchatMessage, fromPeerID: String)
    fun didReceivePrivateMessage(message: BitchatMessage, fromPeerID: String)
    fun didReceiveDeliveryAck(messageID: String, fromPeerID: String)
    fun didReceiveReadReceipt(messageID: String, fromPeerID: String)
    
    // Security events
    fun onSessionEstablished(peerID: String, fingerprint: String)
    fun onHandshakeRequired(peerID: String)
}
```

#### NostrGeohashService Events
```kotlin
// Nostr event callbacks (internal)
private fun handleNostrMessage(giftWrap: NostrEvent)
private fun handleUnifiedGeohashEvent(event: NostrEvent, geohash: String)
private fun handleGeohashDmEvent(giftWrap: NostrEvent, geohash: String, identity: NostrIdentity)

// Public state changes
fun updateGeohashParticipant() → ChatState.setGeohashParticipantCounts()
fun recordGeoParticipant() → ChatState.setGeohashPeople()
```

### Cross-Service Communication

#### Mesh ↔ Nostr Coordination
```kotlin
// Unified DM handling
BluetoothMeshService.sendPrivateMessage() (if connected via mesh)
    ↓ fallback when mesh unavailable
NostrTransport.sendPrivateMessage() (via Nostr relays)

// Identity correlation
NostrGeohashService.findNoiseKeyForNostrPubkey()
    → FavoritesPersistenceService.findNoiseKey()
    → Cross-protocol identity mapping
```

#### Service → UI State Updates
```kotlin
// Mesh service updates
BluetoothMeshService.onPeerConnected()
    → ChatViewModel.updateConnectedPeers()
    → ChatState.setConnectedPeers()
    → UI peer list update

// Nostr service updates  
NostrGeohashService.updateGeohashParticipant()
    → ChatState.setGeohashParticipantCounts()
    → LocationChannelSelector count update
```

---

## 🧩 Manager Specialization Matrix

### Core Managers & Their Domains

| Manager | Primary Domain | Key Responsibilities | Integration Points |
|---------|---------------|---------------------|-------------------|
| **PeerManager** | Peer lifecycle | Discovery, nicknames, fingerprints | SecurityManager, UI state |
| **SecurityManager** | Packet validation | Handshakes, rate limits, trust | EncryptionService, PeerManager |
| **MessageHandler** | Message routing | Type processing, content validation | All message destinations |
| **ConnectionManager** | BLE operations | GATT client/server, scanning | All mesh operations |
| **FragmentManager** | Large messages | Split/reassemble >244 bytes | MessageHandler, ConnectionManager |
| **RelayManager** | Multi-hop routing | TTL management, path optimization | ConnectionManager, SecurityManager |

### UI Managers & Their Domains

| Manager | Primary Domain | Key Responsibilities | Integration Points |
|---------|---------------|---------------------|-------------------|
| **MessageManager** | Message timeline | Display ordering, deduplication | All message sources |
| **PrivateChatManager** | Private messaging | DM state, unread tracking | Mesh + Nostr DMs |
| **ChannelManager** | Channel operations | Join/leave, passwords | Mesh + Nostr channels |
| **DataManager** | State persistence | Settings, configuration storage | All UI state |
| **NotificationManager** | System notifications | Mentions, DMs, geohash alerts | All message sources |

---

## 🔍 Debugging & Tracing

### Service Tracing Points

#### Mesh Message Tracing
```kotlin
// Message send tracing
BluetoothMeshService.sendMessage()
    → MessageHandler.prepareMessage()
    → BinaryProtocol.encode()
    → BluetoothConnectionManager.sendPacket()
    → BluetoothGattClientManager.writeCharacteristic()

// Message receive tracing  
BluetoothGattServerManager.onCharacteristicWriteRequest()
    → BluetoothConnectionManager.handleReceivedData()
    → PacketProcessor.processIncomingPacket()
    → MessageHandler.processMessage()
    → ChatViewModel.didReceiveMessage()
```

#### Encryption Tracing
```kotlin
// Handshake tracing
SecurityManager.handleHandshakePacket()
    → NoiseEncryptionService.processHandshakeMessage()
    → NoiseSession.processHandshakeMessage()
    → Session establishment callback

// Encryption tracing
BluetoothMeshService.sendPrivateMessage()
    → EncryptionService.encrypt()
    → NoiseEncryptionService.encrypt()
    → NoiseSession.encrypt()
    → AES-256-GCM encryption
```

### State Change Tracing

#### Peer State Changes
```kotlin
// Peer connection
BluetoothConnectionManager.onPeerConnected()
    → PeerManager.addPeer()
    → ChatState.setConnectedPeers()
    → UI recomposition

// Nickname updates
MessageHandler.processIdentityAnnouncement()
    → PeerManager.updatePeerNickname()
    → ChatState.setPeerNicknames()
    → MessageComponent sender label update
```

#### Channel State Changes
```kotlin
// Channel switching
ChatViewModel.switchToChannel()
    → ChannelManager.switchToChannel()
    → MessageManager.clearMessages()
    → ChannelManager.loadChannelMessages()
    → ChatState.setCurrentChannel()
    → UI channel indicator update
```

---

## 🛠️ Extension Points

### Custom Component Integration

#### Adding New Message Types
1. **Protocol Layer**: Add to `MessageType` enum in `BinaryProtocol.kt`
2. **Handler Layer**: Add case in `MessageHandler.processMessage()`
3. **Model Layer**: Create data class in `/model` package
4. **UI Layer**: Add rendering in `MessageComponents.kt`

#### Adding New Encryption Modes
1. **Crypto Layer**: Implement in `/crypto` or `/noise` package
2. **Service Layer**: Integrate with `EncryptionService`
3. **Protocol Layer**: Add message type if needed
4. **UI Layer**: Add status indicators

#### Adding New Network Transports
1. **Transport Layer**: Create in `/net` package
2. **Service Layer**: Integrate with `BluetoothMeshService`
3. **Manager Layer**: Add transport-specific manager
4. **UI Layer**: Add connection status indicators

### Plugin Architecture Hooks

#### Message Processing Pipeline
```kotlin
// Pre-processing hooks
interface MessagePreProcessor {
    fun processIncoming(packet: BitchatPacket): ProcessingResult
    fun processOutgoing(message: BitchatMessage): ProcessingResult
}

// Post-processing hooks
interface MessagePostProcessor {
    fun onMessageDisplayed(message: BitchatMessage)
    fun onMessageSent(message: BitchatMessage, result: SendResult)
}
```

#### Encryption Extension Points
```kotlin
// Custom encryption providers
interface EncryptionProvider {
    fun encrypt(data: ByteArray, context: EncryptionContext): ByteArray
    fun decrypt(data: ByteArray, context: EncryptionContext): ByteArray
    fun supportsContext(context: EncryptionContext): Boolean
}
```

---

## 🔗 Cross-Reference Quick Links

### By Functionality

#### **Bluetooth Mesh**
- **Core Service**: [`BluetoothMeshService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt)
- **Connection**: [`BluetoothConnectionManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothConnectionManager.kt)
- **Peers**: [`PeerManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PeerManager.kt)
- **Security**: [`SecurityManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt)
- **Protocol**: [`BinaryProtocol.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt)

#### **Noise Encryption**
- **Main Service**: [`NoiseEncryptionService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseEncryptionService.kt)
- **Session Management**: [`NoiseSessionManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseSessionManager.kt)
- **Individual Sessions**: [`NoiseSession.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseSession.kt)
- **Channel Encryption**: [`NoiseChannelEncryption.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseChannelEncryption.kt)
- **Identity Storage**: [`SecureIdentityStateManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt)

#### **Nostr Integration**
- **Main Service**: [`NostrGeohashService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrGeohashService.kt)
- **Relay Management**: [`NostrRelayManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt)
- **Message Transport**: [`NostrTransport.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrTransport.kt)
- **Protocol**: [`NostrProtocol.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrProtocol.kt)
- **Location**: [`LocationChannelManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/geohash/LocationChannelManager.kt)

#### **UI & State**
- **Main ViewModel**: [`ChatViewModel.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt)
- **State Management**: [`ChatState.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/ChatState.kt)
- **Message Timeline**: [`MessageManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/MessageManager.kt)
- **Private Chats**: [`PrivateChatManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/PrivateChatManager.kt)
- **Main UI**: [`ChatScreen.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/ChatScreen.kt)

### By Protocol Type

#### **Message Types → Handlers**
- **ANNOUNCE** → [`MessageHandler.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt) → [`PeerManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PeerManager.kt)
- **MESSAGE** → [`MessageHandler.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt) → [`MessageManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/MessageManager.kt)
- **NOISE_HANDSHAKE** → [`SecurityManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt) → [`NoiseEncryptionService.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/noise/NoiseEncryptionService.kt)
- **NOISE_ENCRYPTED** → [`MessageHandler.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt) → [`PrivateChatManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/ui/PrivateChatManager.kt)
- **FRAGMENT** → [`FragmentManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt)

#### **Nostr Event Types → Handlers**
- **Kind 20000** (Ephemeral) → [`NostrGeohashService.handleUnifiedGeohashEvent()`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrGeohashService.kt)
- **Kind 1059** (Gift Wrap) → [`NostrGeohashService.handleNostrMessage()`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/nostr/NostrGeohashService.kt)

---

## 🔧 Configuration Cross-References

### Dependency Configuration

#### **Build Dependencies** (`app/build.gradle.kts`)
```kotlin
// Core Android
implementation("androidx.core:core-ktx:1.13.1")
implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.8.4")
implementation("androidx.activity:activity-compose:1.9.1")

// Jetpack Compose
implementation(platform("androidx.compose:compose-bom:2024.06.00"))
implementation("androidx.compose.ui:ui")
implementation("androidx.compose.material3:material3")

// Bluetooth LE
implementation("no.nordicsemi.android:ble:2.7.1")
implementation("no.nordicsemi.android:ble-ktx:2.7.1")

// Cryptography
implementation("org.bouncycastle:bcprov-jdk18on:1.78.1")
implementation("org.bouncycastle:bcpkix-jdk18on:1.78.1")

// Tor integration
implementation("org.torproject:arti-mobile:0.0.4")

// Compression
implementation("org.lz4:lz4-java:1.8.0")
```

#### **Permissions** (`AndroidManifest.xml`)
```xml
<!-- Bluetooth mesh networking -->
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<uses-permission android:name="android.permission.BLUETOOTH_ADVERTISE" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />

<!-- Location for geohash channels -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

<!-- Network for Nostr relays -->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

<!-- Foreground service for background mesh -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_CONNECTED_DEVICE" />
```

---

## 🚀 Performance Integration

### Optimization Coordination

#### **Memory Management**
- **Message Limits**: 1000 messages/geohash, 2000 events cache, 100 peer sessions
- **Cleanup Triggers**: App background, memory pressure, session timeout
- **Cache Strategy**: LRU for UI components, TTL for network data

#### **Network Optimization**
- **BLE Efficiency**: Adaptive scan intervals, connection pooling
- **Nostr Efficiency**: Geohash-specific relays, subscription management
- **Relay Strategy**: Default relays for DMs, geohash relays for channels

#### **Battery Optimization**
- **Power Mode Detection**: [`PowerManager.kt`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/PowerManager.kt) integration
- **Background Limits**: Reduced scanning, delayed operations
- **Foreground Service**: [`BluetoothMeshService`](/Users/tista/codes/bitchat-android/app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt) with notification

---

## 📋 Migration & Upgrade Paths

### Version Compatibility

#### **Protocol Versioning**
- **Binary Protocol**: Version byte in packet header (currently v1)
- **Noise Protocol**: Standard X25519+AES-256-GCM (stable)
- **Nostr Protocol**: NIPs 01, 13, 17 (standard compliance)

#### **Data Migration**
- **Identity Persistence**: SecureIdentityStateManager handles key migration
- **State Migration**: DataManager versioned storage
- **Favorites Migration**: FavoritesPersistenceService cross-version support

### iOS Integration Compatibility

#### **Protocol Compatibility Points**
- **Message Format**: 100% binary compatibility via BinaryProtocol
- **Encryption**: Identical Noise implementation via southernstorm library
- **Peer IDs**: Same 8-byte (16-hex) format and rotation logic
- **Bluetooth UUIDs**: Identical service/characteristic identifiers
- **Nostr Events**: Same kind types, tag formats, encryption schemes

#### **Feature Parity Matrix**
| Feature | Android | iOS | Compatibility |
|---------|---------|-----|---------------|
| Mesh Messaging | ✅ | ✅ | 100% |
| Private Messages | ✅ | ✅ | 100% |
| Channel Encryption | ✅ | ✅ | 100% |
| Geohash Channels | ✅ | ✅ | 100% |
| Nostr DMs | ✅ | ✅ | 100% |
| Peer ID Rotation | ✅ | ✅ | 100% |
| Message Fragmentation | ✅ | ✅ | 100% |
| Packet Signatures | ✅ | ✅ | 100% |
| PoW Mining | ✅ | ✅ | 100% |

---

*Component Cross-Reference v1.2.2 - Updated 2025-08-31*