# BitChat Android - API Reference Guide

> Comprehensive API documentation for BitChat Android components and services

## 📋 Core Services API

### BluetoothMeshService
**Location**: `/app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt`  
**Purpose**: Central mesh networking coordinator with component-based architecture

#### Lifecycle Management
```kotlin
// Service initialization
fun startServices(): Boolean
fun stopServices() 
fun isRunning(): Boolean

// Component coordination
private fun initializeComponents()
private fun shutdownComponents()
```

#### Message Operations
```kotlin
// Public message broadcasting
fun sendMessage(content: String, mentions: List<String> = emptyList(), channel: String? = null)

// Private message encryption
fun sendPrivateMessage(
    content: String, 
    recipientPeerID: String, 
    recipientNickname: String, 
    messageID: String? = null
)

// Message acknowledgments
fun sendReadReceipt(messageID: String, recipientPeerID: String, readerNickname: String)
fun sendDeliveryAck(messageID: String, recipientPeerID: String)
```

#### Peer Management
```kotlin
// Peer information access
fun getPeerNicknames(): Map<String, String>
fun getPeerInfo(peerID: String): PeerInfo?
val myPeerID: String

// Session state queries
fun hasEstablishedSession(peerID: String): Boolean
fun getSessionState(peerID: String): NoiseSessionState
fun shouldShowEncryptionIcon(peerID: String): Boolean
```

#### Identity & Security
```kotlin
// Identity access
fun getIdentityFingerprint(): String
fun getStaticPublicKey(): ByteArray?

// Session management
fun initiateHandshake(peerID: String): ByteArray?
fun processHandshakeMessage(data: ByteArray, peerID: String): ByteArray?
```

#### Diagnostics
```kotlin
fun getDebugStatus(): String
fun clearAllInternalData()
```

---

### EncryptionService
**Location**: `/app/src/main/java/com/bitchat/android/crypto/EncryptionService.kt`  
**Purpose**: Unified encryption interface with Noise Protocol backend

#### Session Management
```kotlin
// Noise session operations
fun hasEstablishedSession(peerID: String): Boolean
fun initiateHandshake(peerID: String): ByteArray?
fun processHandshakeMessage(data: ByteArray, peerID: String): ByteArray?

// Session state
fun getSessionState(peerID: String): NoiseSessionState
fun removePeer(peerID: String)
```

#### Encryption Operations
```kotlin
// Peer-to-peer encryption
fun encrypt(data: ByteArray, peerID: String): ByteArray
fun decrypt(data: ByteArray, peerID: String): ByteArray

// Channel encryption (password-based)
fun setChannelPassword(password: String, channel: String)
fun encryptChannelMessage(message: String, channel: String): ByteArray?
fun decryptChannelMessage(encryptedData: ByteArray, channel: String): String?
fun removeChannelPassword(channel: String)
```

#### Identity & Signatures
```kotlin
// Key access
fun getStaticPublicKey(): ByteArray?
fun getSigningPublicKey(): ByteArray?
fun getIdentityFingerprint(): String

// Digital signatures
fun signData(data: ByteArray): ByteArray?
fun verifyEd25519Signature(signature: ByteArray, data: ByteArray, publicKey: ByteArray): Boolean
```

---

### ChatViewModel
**Location**: `/app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt`  
**Purpose**: MVVM coordinator for UI state and business logic

#### Core Messaging
```kotlin
// Message sending
fun sendMessage(content: String)
fun setNickname(newNickname: String)

// Message management
val messages: StateFlow<List<BitchatMessage>>
val connectedPeers: StateFlow<List<String>>
```

#### Channel Operations
```kotlin
// Channel management
fun joinChannel(channel: String, password: String? = null): Boolean
fun switchToChannel(channel: String?)
fun leaveChannel(channel: String)

// Channel state
val joinedChannels: StateFlow<Set<String>>
val currentChannel: StateFlow<String?>
val channelMessages: StateFlow<Map<String, List<BitchatMessage>>>
```

#### Private Messaging
```kotlin
// Private chat operations
fun startPrivateChat(peerID: String)
fun endPrivateChat()
fun toggleFavorite(peerID: String)

// Private chat state
val privateChats: StateFlow<Map<String, List<BitchatMessage>>>
val selectedPrivateChatPeer: StateFlow<String?>
val unreadPrivateMessages: StateFlow<Set<String>>
```

#### Geohash Integration
```kotlin
// Location channel operations
fun selectLocationChannel(channel: ChannelID)
fun geohashParticipantCount(geohash: String): Int
fun startGeohashDM(pubkeyHex: String)

// Geohash state
val selectedLocationChannel: StateFlow<ChannelID?>
val geohashPeople: StateFlow<List<GeoPerson>>
val isTeleported: StateFlow<Boolean>
```

#### Navigation & UI
```kotlin
// Navigation handling
fun handleBackPressed(): Boolean
fun showSidebar() / hideSidebar()
fun showAppInfo() / hideAppInfo()

// UI state
val showSidebar: StateFlow<Boolean>
val showAppInfo: StateFlow<Boolean>
```

#### Emergency Operations
```kotlin
fun panicClearAllData()
```

---

## 🔐 Noise Protocol API

### NoiseEncryptionService
**Location**: `/app/src/main/java/com/bitchat/android/noise/NoiseEncryptionService.kt`  
**Purpose**: Core Noise protocol implementation with 100% iOS compatibility

#### Identity Management
```kotlin
// Static identity access
fun getStaticPublicKeyData(): ByteArray
fun getSigningPublicKeyData(): ByteArray
fun getIdentityFingerprint(): String

// Peer key access
fun getPeerPublicKeyData(peerID: String): ByteArray?
fun getPeerFingerprint(peerID: String): String?
fun getPeerID(fingerprint: String): String?
```

#### Handshake Protocol
```kotlin
// Handshake initiation
fun initiateHandshake(peerID: String): ByteArray?
fun processHandshakeMessage(data: ByteArray, peerID: String): ByteArray?

// Session state
fun hasEstablishedSession(peerID: String): Boolean
fun getSessionState(peerID: String): NoiseSessionState
```

#### Encryption Transport
```kotlin
// Message encryption/decryption
fun encrypt(data: ByteArray, peerID: String): ByteArray?
fun decrypt(encryptedData: ByteArray, peerID: String): ByteArray?

// Channel encryption
fun setChannelPassword(password: String, channel: String)
fun encryptChannelMessage(message: String, channel: String): ByteArray?
fun decryptChannelMessage(encryptedData: ByteArray, channel: String): String?
```

#### Session Maintenance
```kotlin
// Session lifecycle
fun removePeer(peerID: String)
fun updatePeerIDMapping(oldPeerID: String?, newPeerID: String, fingerprint: String)

// Rekey operations
fun getSessionsNeedingRekey(): List<String>
fun initiateRekey(peerID: String): ByteArray?
```

#### Packet Signing
```kotlin
// Digital signatures
fun signPacket(packet: BitchatPacket): BitchatPacket?
fun verifyPacketSignature(packet: BitchatPacket, publicKey: ByteArray): Boolean
fun signData(data: ByteArray): ByteArray?
fun verifySignature(signature: ByteArray, data: ByteArray, publicKey: ByteArray): Boolean
```

#### Emergency Operations
```kotlin
fun clearPersistentIdentity()
fun shutdown()
```

### NoiseSession
**Location**: `/app/src/main/java/com/bitchat/android/noise/NoiseSession.kt`  
**Purpose**: Individual peer session state management

#### Session States
```kotlin
enum class NoiseSessionState {
    NOT_INITIALIZED,
    HANDSHAKING,
    ESTABLISHED,
    FAILED
}
```

#### Session Operations
```kotlin
// Session lifecycle
fun initiateHandshake(): ByteArray?
fun processHandshakeMessage(message: ByteArray): ByteArray?
fun isEstablished(): Boolean

// Message encryption
fun encrypt(plaintext: ByteArray): ByteArray?
fun decrypt(ciphertext: ByteArray): ByteArray?

// Session metadata
fun getRemoteStaticKey(): ByteArray?
fun needsRekey(): Boolean
fun destroy()
```

---

## 🌐 Nostr Protocol API

### NostrGeohashService
**Location**: `/app/src/main/java/com/bitchat/android/nostr/NostrGeohashService.kt`  
**Purpose**: Nostr relay integration for geohash channels and DMs

#### Initialization
```kotlin
// Service setup
fun initializeNostrIntegration()
fun initializeLocationChannelState()
fun panicResetNostrAndGeohash()
```

#### Geohash Messaging
```kotlin
// Channel messaging
fun sendGeohashMessage(
    content: String, 
    channel: GeohashChannel, 
    myPeerID: String, 
    nickname: String?
)

// Message history
fun loadGeohashMessages(geohash: String)
fun clearGeohashMessageHistory()
```

#### Participant Tracking
```kotlin
// Participant management
fun geohashParticipantCount(geohash: String): Int
fun beginGeohashSampling(geohashes: List<String>)
fun endGeohashSampling()

// Participant queries
fun isPersonTeleported(pubkeyHex: String): Boolean
fun displayNameForNostrPubkeyUI(pubkeyHex: String): String
fun mentionHandleForNostrPubkey(pubkeyHex: String): String
```

#### Location Channel Control
```kotlin
fun selectLocationChannel(channel: ChannelID)
```

#### Direct Messaging
```kotlin
// Geohash DM operations
fun startGeohashDM(pubkeyHex: String, onStartPrivateChat: (String) -> Unit)
fun sendNostrGeohashDM(content: String, recipientPeerID: String, messageID: String, myPeerID: String)
fun sendNostrGeohashReadReceipt(messageID: String, recipientPeerID: String, myPeerID: String)

// Key mapping
fun getNostrKeyMapping(): Map<String, String>
```

#### User Management
```kotlin
// Blocking system
fun blockUserInGeohash(targetNickname: String)

// Color system
fun colorForNostrPubkey(pubkeyHex: String, isDark: Boolean): Color
```

### NostrRelayManager
**Location**: `/app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt`  
**Purpose**: WebSocket relay connection management

#### Connection Management
```kotlin
// Relay lifecycle
fun connect()
fun disconnect()
fun reconnect()

// Connection state
fun isConnected(): Boolean
fun getConnectionStatus(): ConnectionStatus
```

#### Subscription Management
```kotlin
// Standard subscriptions
fun subscribe(
    filter: NostrFilter,
    id: String,
    handler: (NostrEvent) -> Unit,
    targetRelayUrls: List<String>? = null
)

// Geohash-specific subscriptions
fun subscribeForGeohash(
    geohash: String,
    filter: NostrFilter,
    id: String,
    handler: (NostrEvent) -> Unit,
    includeDefaults: Boolean = true,
    nRelays: Int = 5
)

// Subscription cleanup
fun unsubscribe(subscriptionId: String)
fun clearAllSubscriptions()
```

#### Event Publishing
```kotlin
// Message sending
fun sendEvent(event: NostrEvent, targetRelayUrls: List<String>? = null)
fun sendEventToGeohash(event: NostrEvent, geohash: String, includeDefaults: Boolean = true, nRelays: Int = 5)
```

### NostrTransport
**Location**: `/app/src/main/java/com/bitchat/android/nostr/NostrTransport.kt`  
**Purpose**: High-level message routing via Nostr relays

#### Private Messaging
```kotlin
// Standard private messages
fun sendPrivateMessage(content: String, toRecipientHex: String, fromIdentity: NostrIdentity, messageID: String)

// Geohash private messages
fun sendPrivateMessageGeohash(content: String, toRecipientHex: String, fromIdentity: NostrIdentity, messageID: String)
```

#### Message Acknowledgments
```kotlin
// Delivery acknowledgments
fun sendDeliveryAck(messageID: String, recipientPeerID: String)
fun sendDeliveryAckGeohash(messageID: String, toRecipientHex: String, fromIdentity: NostrIdentity)

// Read receipts
fun sendReadReceipt(readReceipt: ReadReceipt, recipientPeerID: String)
fun sendReadReceiptGeohash(messageID: String, toRecipientHex: String, fromIdentity: NostrIdentity)
```

#### Identity Configuration
```kotlin
var senderPeerID: String?
```

---

## 📡 Bluetooth Mesh API

### PeerManager
**Location**: `/app/src/main/java/com/bitchat/android/mesh/PeerManager.kt`  
**Purpose**: Peer lifecycle management and identity verification

#### Peer Discovery
```kotlin
// Peer lifecycle
fun addPeer(peerID: String, deviceAddress: String, deviceName: String?, rssi: Int)
fun updatePeerNickname(peerID: String, nickname: String)
fun removePeer(peerID: String, reason: String)

// Peer queries
fun getAllPeers(): Map<String, PeerInfo>
fun getPeerInfo(peerID: String): PeerInfo?
fun getPeerNicknames(): Map<String, String>
fun isKnownPeer(peerID: String): Boolean
```

#### Identity Verification
```kotlin
// Fingerprint management
fun storeFingerprintForPeer(peerID: String, publicKey: ByteArray)
fun getFingerprintForPeer(peerID: String): String?
fun shouldTrustPeer(peerID: String): Boolean

// Peer ID rotation
fun updatePeerIDMapping(oldPeerID: String?, newPeerID: String, fingerprint: String)
```

#### Peer Filtering
```kotlin
// Filtering system
fun shouldFilterPeer(peerID: String): Boolean
fun shouldFilterMessage(content: String, fromPeerID: String): Boolean
```

### BluetoothConnectionManager
**Location**: `/app/src/main/java/com/bitchat/android/mesh/BluetoothConnectionManager.kt`  
**Purpose**: BLE GATT client/server coordination

#### Connection Lifecycle
```kotlin
// Service management
fun startServices(): Boolean
fun stopServices()

// Connection operations
fun connectToPeer(deviceAddress: String, peerID: String)
fun disconnectFromPeer(deviceAddress: String)
fun disconnectAll()
```

#### Device Discovery
```kotlin
// Scanning operations
fun startScanning()
fun stopScanning()
fun isScanning(): Boolean

// Discovery callbacks
interface BluetoothConnectionManagerDelegate {
    fun onPeerDiscovered(peerID: String, deviceAddress: String, rssi: Int)
    fun onPeerConnected(peerID: String, deviceAddress: String)
    fun onPeerDisconnected(peerID: String, deviceAddress: String)
}
```

#### Data Transmission
```kotlin
// Packet sending
fun sendPacket(packet: BitchatPacket, targetPeerID: String? = null)
fun sendToAllConnectedPeers(packet: BitchatPacket)

// Connection info
fun getConnectedDeviceAddresses(): Set<String>
fun getConnectedPeerIDs(): Set<String>
fun getRSSIForPeer(peerID: String): Int?
```

### SecurityManager
**Location**: `/app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt`  
**Purpose**: Packet validation and Noise handshake coordination

#### Packet Validation
```kotlin
// Security validation
fun validateIncomingPacket(packet: BitchatPacket, fromPeerID: String): ValidationResult
fun shouldAcceptPacket(packet: BitchatPacket, fromPeerID: String): Boolean

// Handshake management
fun handleHandshakePacket(packet: BitchatPacket, fromPeerID: String): ByteArray?
fun isHandshakeComplete(peerID: String): Boolean
```

#### Security Policies
```kotlin
// Rate limiting
fun checkRateLimit(peerID: String): Boolean
fun updateRateLimit(peerID: String)

// Trust management
fun establishTrust(peerID: String, publicKey: ByteArray)
fun revokeTrust(peerID: String)
```

---

## 🌍 Geohash & Location API

### LocationChannelManager
**Location**: `/app/src/main/java/com/bitchat/android/geohash/LocationChannelManager.kt`  
**Purpose**: Location permissions and geohash channel computation

#### Permission Management
```kotlin
// Permission operations
fun enableLocationChannels()
fun enableLocationServices()
fun disableLocationServices()
fun isLocationServicesEnabled(): Boolean

// Permission state
enum class PermissionState {
    NOT_DETERMINED, DENIED, RESTRICTED, AUTHORIZED
}
val permissionState: LiveData<PermissionState>
```

#### Channel Management
```kotlin
// Channel operations
fun refreshChannels()
fun select(channel: ChannelID)
fun clearPersistedChannel()

// Channel state
val availableChannels: LiveData<List<GeohashChannel>>
val selectedChannel: LiveData<ChannelID>
val teleported: LiveData<Boolean>
val locationNames: LiveData<Map<GeohashChannelLevel, String>>
```

#### Live Updates
```kotlin
// Periodic refresh
fun beginLiveRefresh(interval: Long = 5000L)
fun endLiveRefresh()

// Loading state
val isLoadingLocation: LiveData<Boolean>
val locationServicesEnabled: LiveData<Boolean>
```

#### Resource Management
```kotlin
fun cleanup()
```

### GeohashChannel Classes
**Location**: `/app/src/main/java/com/bitchat/android/geohash/GeohashChannel.kt`

#### Channel Types
```kotlin
sealed class ChannelID {
    object Mesh : ChannelID()
    data class Location(val channel: GeohashChannel) : ChannelID()
    
    val displayName: String
}

data class GeohashChannel(
    val level: GeohashChannelLevel,
    val geohash: String
)

enum class GeohashChannelLevel(val precision: Int, val displayName: String) {
    REGION(2, "Region"),
    PROVINCE(4, "Province"),
    CITY(5, "City"),
    NEIGHBORHOOD(6, "Neighborhood"),
    BLOCK(7, "Block")
}
```

---

## 🔒 Identity & Storage API

### SecureIdentityStateManager
**Location**: `/app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt`  
**Purpose**: Persistent identity storage with Android Keystore

#### Key Management
```kotlin
// Static identity keys
fun loadStaticKey(): Pair<ByteArray, ByteArray>?
fun saveStaticKey(privateKey: ByteArray, publicKey: ByteArray)

// Signing keys
fun loadSigningKey(): Pair<ByteArray, ByteArray>?
fun saveSigningKey(privateKey: ByteArray, publicKey: ByteArray)
```

#### Identity Validation
```kotlin
// Key validation
fun validatePublicKey(publicKey: ByteArray): Boolean
fun validatePrivateKey(privateKey: ByteArray): Boolean
fun generateFingerprint(publicKeyData: ByteArray): String
fun isValidFingerprint(fingerprint: String): Boolean
```

#### Peer ID Rotation
```kotlin
// Rotation management
fun shouldRotatePeerID(): Boolean
fun markRotationCompleted()
fun getTimeUntilNextRotation(): Long
```

#### Secure Storage
```kotlin
// Generic secure storage
fun storeSecureValue(key: String, value: String)
fun getSecureValue(key: String): String?
fun removeSecureValue(key: String)
fun hasSecureValue(key: String): Boolean
fun clearSecureValues(vararg keys: String)
```

#### Emergency Operations
```kotlin
fun clearIdentityData()
fun hasIdentityData(): Boolean
fun getDebugInfo(): String
```

---

## 📦 Protocol & Data Models API

### BitchatPacket
**Location**: `/app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt`  
**Purpose**: Binary packet format with iOS compatibility

#### Packet Structure
```kotlin
data class BitchatPacket(
    val version: UByte = 1u,
    val type: UByte,
    val senderID: ByteArray,
    val recipientID: ByteArray? = null,
    val timestamp: ULong,
    val payload: ByteArray,
    var signature: ByteArray? = null,
    var ttl: UByte
)
```

#### Serialization
```kotlin
// Binary operations
fun toBinaryData(): ByteArray?
fun toBinaryDataForSigning(): ByteArray?

companion object {
    fun fromBinaryData(data: ByteArray): BitchatPacket?
}
```

#### Message Types
```kotlin
enum class MessageType(val value: UByte) {
    ANNOUNCE(0x01u),
    MESSAGE(0x02u),
    LEAVE(0x03u),
    NOISE_HANDSHAKE(0x10u),
    NOISE_ENCRYPTED(0x11u),
    FRAGMENT(0x20u)
}
```

### BitchatMessage
**Location**: `/app/src/main/java/com/bitchat/android/model/BitchatMessage.kt`  
**Purpose**: Primary message data structure

#### Message Structure
```kotlin
data class BitchatMessage(
    val id: String,
    val sender: String,
    val content: String,
    val timestamp: Date,
    val isRelay: Boolean = false,
    val originalSender: String? = null,
    val isPrivate: Boolean = false,
    val recipientNickname: String? = null,
    val senderPeerID: String? = null,
    val mentions: List<String>? = null,
    val channel: String? = null,
    val encryptedContent: ByteArray? = null,
    val isEncrypted: Boolean = false,
    val deliveryStatus: DeliveryStatus? = null,
    val powDifficulty: Int? = null
)
```

#### Serialization
```kotlin
// Binary operations
fun toBinaryPayload(): ByteArray?

companion object {
    fun fromBinaryPayload(data: ByteArray): BitchatMessage?
}
```

#### Delivery Status
```kotlin
sealed class DeliveryStatus {
    object Sending : DeliveryStatus()
    object Sent : DeliveryStatus()
    data class Delivered(val to: String, val at: Date) : DeliveryStatus()
    data class Read(val by: String, val at: Date) : DeliveryStatus()
    data class Failed(val reason: String) : DeliveryStatus()
    data class PartiallyDelivered(val reached: Int, val total: Int) : DeliveryStatus()
    
    fun getDisplayText(): String
}
```

---

## 🎨 UI Components API

### ChatState
**Location**: `/app/src/main/java/com/bitchat/android/ui/ChatState.kt`  
**Purpose**: Centralized reactive state management

#### Core State Properties
```kotlin
// Message state
val messages: LiveData<List<BitchatMessage>>
val connectedPeers: LiveData<List<String>>
val nickname: LiveData<String>
val isConnected: LiveData<Boolean>

// Private chat state
val privateChats: LiveData<Map<String, List<BitchatMessage>>>
val selectedPrivateChatPeer: LiveData<String?>
val unreadPrivateMessages: LiveData<Set<String>>

// Channel state
val joinedChannels: LiveData<Set<String>>
val currentChannel: LiveData<String?>
val channelMessages: LiveData<Map<String, List<BitchatMessage>>>
val unreadChannelMessages: LiveData<Map<String, Int>>
```

#### UI State
```kotlin
// Navigation state
val showSidebar: LiveData<Boolean>
val showAppInfo: LiveData<Boolean>

// Command system
val showCommandSuggestions: LiveData<Boolean>
val commandSuggestions: LiveData<List<CommandSuggestion>>
val showMentionSuggestions: LiveData<Boolean>
val mentionSuggestions: LiveData<List<String>>
```

#### Geohash State
```kotlin
// Location channel state
val selectedLocationChannel: LiveData<ChannelID?>
val isTeleported: LiveData<Boolean>
val geohashPeople: LiveData<List<GeoPerson>>
val teleportedGeo: LiveData<Set<String>>
val geohashParticipantCounts: LiveData<Map<String, Int>>
```

#### State Accessors & Mutators
```kotlin
// Value getters (synchronous access)
fun getMessagesValue(): List<BitchatMessage>
fun getConnectedPeersValue(): List<String>
fun getNicknameValue(): String?
// ... (all properties have getValue() methods)

// State setters (reactive updates)
fun setMessages(messages: List<BitchatMessage>)
fun setConnectedPeers(peers: List<String>)
fun setNickname(nickname: String)
// ... (all properties have set() methods)
```

### MessageManager
**Location**: `/app/src/main/java/com/bitchat/android/ui/MessageManager.kt`  
**Purpose**: Message timeline and display management

#### Message Operations
```kotlin
// Message management
fun addMessage(message: BitchatMessage)
fun updateMessage(messageID: String, updater: (BitchatMessage) -> BitchatMessage)
fun removeMessage(messageID: String)
fun clearMessages()

// Message queries
fun findMessage(messageID: String): BitchatMessage?
fun getMessagesForPeer(peerID: String): List<BitchatMessage>
```

#### Content Processing
```kotlin
// Message parsing
fun parseMentions(content: String, peerNicknames: Map<String, String>, senderNickname: String?): List<String>
fun processSystemCommand(content: String): Boolean
```

### PrivateChatManager
**Location**: `/app/src/main/java/com/bitchat/android/ui/PrivateChatManager.kt`  
**Purpose**: Private messaging coordination

#### Chat Management
```kotlin
// Chat operations
fun startPrivateChat(peerID: String)
fun handleIncomingPrivateMessage(message: BitchatMessage, suppressUnread: Boolean = false)
fun markChatAsRead(peerID: String)

// Chat state
fun hasUnreadMessages(peerID: String): Boolean
fun getUnreadCount(peerID: String): Int
```

### NotificationManager
**Location**: `/app/src/main/java/com/bitchat/android/ui/NotificationManager.kt`  
**Purpose**: System notification handling

#### Notification Operations
```kotlin
// Mesh notifications
fun showPrivateMessageNotification(senderNickname: String, messageContent: String, senderPeerID: String)
fun showMeshMentionNotification(senderNickname: String, messageContent: String)

// Geohash notifications
fun showGeohashNotification(
    geohash: String,
    senderNickname: String,
    messageContent: String,
    isMention: Boolean,
    isFirstMessage: Boolean,
    locationName: String?
)

// Notification management
fun setCurrentGeohash(geohash: String?)
fun clearNotificationsForGeohash(geohash: String)
fun clearMeshMentionNotifications()
```

---

## ⚙️ Utility & Service APIs

### DataManager
**Location**: `/app/src/main/java/com/bitchat/android/ui/DataManager.kt`  
**Purpose**: State persistence and configuration storage

#### State Persistence
```kotlin
// Core state
fun saveNickname(nickname: String)
fun loadNickname(): String?
fun saveMyPeerID(peerID: String)
fun loadMyPeerID(): String?

// Channel state
fun saveLastGeohashChannel(channelData: String)
fun loadLastGeohashChannel(): String?
fun clearLastGeohashChannel()

// Location state
fun saveLocationServicesEnabled(enabled: Boolean)
fun isLocationServicesEnabled(): Boolean
```

#### User Management
```kotlin
// Blocking system
fun addGeohashBlockedUser(pubkeyHex: String)
fun removeGeohashBlockedUser(pubkeyHex: String)
fun isGeohashUserBlocked(pubkeyHex: String): Boolean
fun getGeohashBlockedUsers(): Set<String>
```

### ChannelManager
**Location**: `/app/src/main/java/com/bitchat/android/ui/ChannelManager.kt`  
**Purpose**: Channel operations and password management

#### Channel Operations
```kotlin
// Channel management
fun joinChannel(channelName: String, password: String? = null): Boolean
fun leaveChannel(channelName: String)
fun switchToChannel(channelName: String?)

// Password operations
fun setChannelPassword(channelName: String, password: String)
fun hasChannelPassword(channelName: String): Boolean
fun removeChannelPassword(channelName: String)
```

### CommandProcessor
**Location**: `/app/src/main/java/com/bitchat/android/ui/CommandProcessor.kt`  
**Purpose**: IRC-style command processing

#### Command Execution
```kotlin
// Command processing
fun processCommand(input: String, currentChannel: String?): CommandResult
fun getAvailableCommands(): List<CommandSuggestion>
fun parseCommand(input: String): ParsedCommand?

// Command suggestions
fun getCommandSuggestions(prefix: String): List<CommandSuggestion>
```

#### Supported Commands
```kotlin
// Core commands
/join <channel> [password]     // Join channel
/leave <channel>               // Leave channel  
/nick <nickname>               // Set nickname
/dm <peer>                     // Start private chat
/block <nickname>              // Block user
/clear                         // Clear messages
/panic                         // Emergency data clear
/help [command]                // Command help
```

---

## 🔧 Platform Integration API

### TorManager
**Location**: `/app/src/main/java/com/bitchat/android/net/TorManager.kt`  
**Purpose**: Tor network integration for relay connections

#### Tor Operations
```kotlin
// Tor lifecycle
fun startTor(): Boolean
fun stopTor()
fun isTorRunning(): Boolean

// Proxy configuration
fun getProxySettings(): ProxySettings?
fun isProxyReady(): Boolean
```

### RelayDirectoryService
**Location**: `/app/src/main/java/com/bitchat/android/net/RelayDirectoryService.kt`  
**Purpose**: Nostr relay discovery and management

#### Relay Management
```kotlin
// Relay discovery
fun discoverRelays(): List<String>
fun validateRelay(relayUrl: String): Boolean
fun updateRelayList()

// Geohash-specific relays
fun getRelaysForGeohash(geohash: String): List<String>
fun cacheGeohashRelays(geohash: String, relays: List<String>)
```

---

## 🧪 Testing & Debug API

### Debug Interfaces

#### Component Debug Info
```kotlin
// Service debug status
fun BluetoothMeshService.getDebugStatus(): String
fun PeerManager.getDebugInfo(): String
fun SecurityManager.getDebugInfo(): String
fun NoiseEncryptionService.getDebugInfo(): String
```

#### Connection Diagnostics
```kotlin
// Connection metrics
fun getConnectionMetrics(): ConnectionMetrics
fun getPeerConnectionHistory(peerID: String): List<ConnectionEvent>
fun getSignalStrengthHistory(peerID: String): List<RSSIReading>
```

#### Session Diagnostics
```kotlin
// Session state
fun getActiveSessionCount(): Int
fun getSessionDebugInfo(peerID: String): SessionDebugInfo
fun getHandshakeFailureReasons(): Map<String, String>
```

---

## 🚨 Error Handling

### Exception Types

#### Noise Protocol Errors
```kotlin
sealed class NoiseEncryptionError(message: String) : Exception(message) {
    object HandshakeRequired : NoiseEncryptionError("Handshake required before encryption")
    object SessionNotEstablished : NoiseEncryptionError("No established Noise session")
    object InvalidMessage : NoiseEncryptionError("Invalid message format")
    class HandshakeFailed(cause: Throwable) : NoiseEncryptionError("Handshake failed: ${cause.message}")
}
```

#### Mesh Network Errors
```kotlin
sealed class MeshNetworkError(message: String) : Exception(message) {
    object BluetoothDisabled : MeshNetworkError("Bluetooth is disabled")
    object PermissionDenied : MeshNetworkError("Bluetooth permission denied")
    object ServiceNotStarted : MeshNetworkError("Mesh service not started")
    class ConnectionFailed(peerID: String) : MeshNetworkError("Failed to connect to $peerID")
}
```

---

## 📊 Performance Considerations

### Resource Usage Guidelines

#### Memory Management
- **Session Limit**: 100 concurrent peer sessions
- **Message History**: 1000 messages per geohash, 2000 private messages per peer
- **Cache Sizes**: 2000 processed Nostr events, 100 cached relay responses

#### Network Optimization
- **BLE Scanning**: Adaptive duty cycle based on peer count
- **Packet Relay**: TTL-based forwarding to prevent loops
- **Nostr Subscriptions**: Geohash-specific relay selection for efficiency

#### Battery Optimization
- **Power Mode Integration**: Reduces scan frequency in battery saver mode
- **Background Limits**: Minimal operations when app backgrounded
- **Connection Pooling**: Reuse BLE connections across message sends

---

## 🔗 Integration Examples

### Basic Message Flow
```kotlin
// 1. Initialize services
val meshService = BluetoothMeshService(context)
meshService.startServices()

// 2. Send message
meshService.sendMessage("Hello mesh!", emptyList(), null)

// 3. Handle incoming
override fun didReceiveMessage(message: BitchatMessage, fromPeerID: String) {
    chatViewModel.handleIncomingMessage(message)
}
```

### Private Message Flow
```kotlin
// 1. Start private chat
chatViewModel.startPrivateChat(peerID)

// 2. Send encrypted message
meshService.sendPrivateMessage("Private hello!", peerID, "Alice", messageID)

// 3. Handle delivery status
override fun didReceiveDeliveryAck(messageID: String, fromPeerID: String) {
    chatViewModel.updateDeliveryStatus(messageID, DeliveryStatus.Delivered("Alice", Date()))
}
```

### Geohash Channel Flow
```kotlin
// 1. Enable location
locationManager.enableLocationChannels()
locationManager.enableLocationServices()

// 2. Select channel
val cityChannel = ChannelID.Location(GeohashChannel(GeohashChannelLevel.CITY, "9q8yy"))
chatViewModel.selectLocationChannel(cityChannel)

// 3. Send geohash message
nostrService.sendGeohashMessage("Hello city!", cityChannel.channel, myPeerID, "Alice")
```

---

*API Reference v1.2.2 - Updated 2025-08-31*