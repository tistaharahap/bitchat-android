# BitChat Android - Documentation Index

> Master navigation guide for BitChat Android development documentation

## 📚 Documentation Overview

This documentation suite provides comprehensive guidance for understanding, developing, and extending the BitChat Android secure P2P messaging application.

### Documentation Structure

| Document | Purpose | Audience |
|----------|---------|----------|
| **[Architecture Index](./ARCHITECTURE_INDEX.md)** | High-level system overview | Architects, New developers |
| **[API Reference](./API_REFERENCE.md)** | Detailed API documentation | Developers, Integrators |
| **[Component Cross-Reference](./COMPONENT_CROSSREF.md)** | Integration patterns & relationships | Senior developers, Debuggers |

---

## 🚀 Quick Start Navigation

### For New Developers
1. **Start Here**: [Architecture Index](./ARCHITECTURE_INDEX.md) - Project overview and system design
2. **Understand APIs**: [API Reference](./API_REFERENCE.md) - Core service interfaces
3. **Learn Integration**: [Component Cross-Reference](./COMPONENT_CROSSREF.md) - How components work together

### For Specific Tasks

#### **Adding New Features**
1. **Architecture Index** → Component relationships & design patterns
2. **API Reference** → Existing service interfaces to extend
3. **Component Cross-Reference** → Integration points and extension hooks

#### **Debugging Issues**
1. **Component Cross-Reference** → Service tracing points and data flow
2. **API Reference** → Debug interfaces and diagnostic methods
3. **Architecture Index** → System boundaries and validation points

#### **Security Review**
1. **Architecture Index** → Encryption architecture and security layers
2. **API Reference** → Cryptographic APIs and security interfaces
3. **Component Cross-Reference** → Trust establishment and validation flows

#### **Performance Optimization**
1. **Component Cross-Reference** → Performance integration points
2. **API Reference** → Resource management interfaces
3. **Architecture Index** → Scalability considerations

---

## 🏗️ System Architecture Quick Reference

### Core Service Architecture
```
BitchatApplication (entry point)
├── MainActivity (UI host, permissions)
├── ChatViewModel (MVVM coordinator)
├── BluetoothMeshService (mesh networking)
├── NostrGeohashService (location channels)
└── EncryptionService (crypto facade)
```

### Key Integration Points
- **Mesh ↔ UI**: BluetoothMeshDelegate callbacks → ChatState updates
- **Nostr ↔ UI**: NostrGeohashService → ChatState reactive updates
- **Encryption ↔ Mesh**: NoiseEncryptionService → SecurityManager coordination
- **Storage ↔ All**: DataManager persistence across all components

---

## 🔑 Key Concepts

### Protocol Compatibility
- **100% iOS Compatible**: Binary protocol, encryption, message formats
- **Cross-Platform DMs**: Noise-encrypted private messages work across platforms
- **Unified Identity**: Same fingerprint system and peer ID rotation logic

### Security Architecture
- **Transport Security**: Noise Protocol (X25519 + AES-256-GCM)
- **Authentication**: Ed25519 packet signatures
- **Channel Security**: Argon2id password-based encryption
- **Identity Privacy**: Peer ID rotation, traffic analysis resistance

### Network Design
- **Bluetooth Mesh**: Multi-hop relay with adaptive scanning
- **Nostr Integration**: Geohash channels via ephemeral events
- **Dual Transport**: Mesh for local, Nostr for global reach

---

## 📋 Development Workflows

### Common Development Patterns

#### **Adding Mesh Features**
1. **Protocol**: Define message types in `BinaryProtocol.kt`
2. **Handler**: Add processing in `MessageHandler.kt`
3. **Service**: Implement in `BluetoothMeshService.kt`
4. **UI**: Add interface in `ChatViewModel.kt` and UI components

#### **Adding Encryption Features**
1. **Core**: Implement in `NoiseEncryptionService.kt`
2. **Facade**: Expose via `EncryptionService.kt`
3. **Integration**: Connect to `SecurityManager.kt`
4. **UI**: Add status indicators in UI components

#### **Adding UI Features**
1. **State**: Define in `ChatState.kt`
2. **Logic**: Implement in `ChatViewModel.kt`
3. **Components**: Create in UI package
4. **Integration**: Connect to service layer

### Testing Strategy

#### **Unit Testing Focus**
- **Protocol Logic**: Message encoding/decoding, validation
- **Encryption**: Handshake flows, session management
- **State Management**: UI state transitions, reactive updates
- **Peer Management**: Lifecycle events, identity verification

#### **Integration Testing Focus**
- **Cross-Platform**: iOS compatibility validation
- **End-to-End**: Message send/receive across all transports
- **Security**: Encryption across service boundaries
- **UI Flows**: Complete user interaction sequences

---

## 🔍 Debugging Guide

### Common Debugging Scenarios

#### **Message Not Received**
1. **Check**: [BluetoothMeshService debug](./API_REFERENCE.md#diagnostics) status
2. **Trace**: [Message flow](./COMPONENT_CROSSREF.md#incoming-message-flow) through components
3. **Validate**: [Security validation](./COMPONENT_CROSSREF.md#message-validation-pipeline) pipeline

#### **Encryption Failing**
1. **Check**: [NoiseEncryptionService session](./API_REFERENCE.md#session-management) state
2. **Trace**: [Handshake flow](./COMPONENT_CROSSREF.md#trust-establishment-flow) completion
3. **Validate**: [Key management](./API_REFERENCE.md#identity--signatures) and fingerprints

#### **UI Not Updating**
1. **Check**: [ChatState reactive](./API_REFERENCE.md#state-accessors--mutators) updates
2. **Trace**: [Service → UI](./COMPONENT_CROSSREF.md#service--ui-state-updates) event flow
3. **Validate**: [State manager](./COMPONENT_CROSSREF.md#ui-managers--their-domains) coordination

### Debug Information Sources

#### **Service Debug APIs**
```kotlin
meshService.getDebugStatus()           // Overall mesh status
peerManager.getDebugInfo()             // Peer state details
encryptionService.getDebugInfo()       // Session information
identityManager.getDebugInfo()         // Identity state
```

#### **Component Tracing**
- **Message Tracing**: [Mesh message flow](./COMPONENT_CROSSREF.md#mesh-message-tracing)
- **Encryption Tracing**: [Handshake and encryption](./COMPONENT_CROSSREF.md#encryption-tracing)
- **State Tracing**: [Peer and channel state](./COMPONENT_CROSSREF.md#state-change-tracing)

---

## 🔗 External Resources

### Protocol Specifications
- **[Noise Protocol](https://noiseprotocol.org)** - Cryptographic protocol specification
- **[Nostr NIPs](https://github.com/nostr-protocol/nips)** - Nostr protocol specifications
- **[BLE Specification](https://www.bluetooth.com/specifications/specs/)** - Bluetooth Low Energy standards

### Android Development
- **[Jetpack Compose](https://developer.android.com/jetpack/compose)** - Modern UI toolkit
- **[BLE Guide](https://developer.android.com/guide/topics/connectivity/bluetooth)** - Android Bluetooth development
- **[MVVM Architecture](https://developer.android.com/topic/architecture)** - Android architecture guidelines

### Cryptography Resources
- **[BouncyCastle](https://www.bouncycastle.org/documentation.html)** - Cryptography library documentation
- **[RFC 7539](https://tools.ietf.org/html/rfc7539)** - ChaCha20-Poly1305 specification
- **[RFC 8439](https://tools.ietf.org/html/rfc8439)** - ChaCha20-Poly1305 for IETF protocols

---

## 🤝 Contributing Guidelines

### Documentation Updates

#### **When to Update Documentation**
- **API Changes**: Update [API Reference](./API_REFERENCE.md)
- **Architecture Changes**: Update [Architecture Index](./ARCHITECTURE_INDEX.md)
- **Integration Changes**: Update [Component Cross-Reference](./COMPONENT_CROSSREF.md)

#### **Documentation Standards**
- **Code Examples**: Include working Kotlin code snippets
- **Cross-References**: Link between related components and APIs
- **Version Tracking**: Update version numbers and dates
- **iOS Compatibility**: Note any cross-platform implications

### Code Review Checklist

#### **Architecture Compliance**
- [ ] Follows established service boundaries
- [ ] Maintains iOS protocol compatibility
- [ ] Uses proper dependency injection patterns
- [ ] Implements security best practices

#### **Documentation Updates**
- [ ] API changes documented
- [ ] Integration patterns updated
- [ ] Cross-references maintained
- [ ] Version information current

---

## 📞 Support & Contact

### Getting Help

#### **Documentation Issues**
- **Missing Information**: Create issue with specific gaps
- **Incorrect Details**: Submit correction with evidence
- **Integration Questions**: Provide specific use case context

#### **Development Support**
- **Architecture Questions**: Reference [Architecture Index](./ARCHITECTURE_INDEX.md) first
- **API Usage**: Check [API Reference](./API_REFERENCE.md) for examples
- **Integration Issues**: Consult [Component Cross-Reference](./COMPONENT_CROSSREF.md) for patterns

---

*Documentation Index v1.2.2 - Updated 2025-08-31*