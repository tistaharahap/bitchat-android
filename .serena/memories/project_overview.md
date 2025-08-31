# BitChat Android - Project Overview

## Purpose
BitChat is a secure, decentralized, peer-to-peer messaging app for Android that works over Bluetooth mesh networks. It's the Android port of the original iOS bitchat app, maintaining 100% protocol compatibility for cross-platform communication.

## Key Features
- Cross-platform compatible with iOS bitchat
- Decentralized Bluetooth LE mesh networking
- End-to-end encryption (X25519 + AES-256-GCM)
- Channel-based group messaging with password protection
- Store & forward messaging for offline peers
- No accounts, phone numbers, or persistent identifiers
- IRC-style commands (/join, /msg, /who)
- Modern Jetpack Compose UI with Material Design 3
- Tor integration for enhanced privacy
- Nostr protocol support for geohash channels

## Tech Stack
- **Language**: Kotlin (primary), Java (Noise crypto library)
- **UI**: Jetpack Compose with Material Design 3
- **Architecture**: MVVM with AndroidViewModel
- **Networking**: Bluetooth LE via Nordic BLE library
- **Encryption**: BouncyCastle, Google Tink, Noise Protocol
- **Storage**: EncryptedSharedPreferences for secure local storage
- **Async**: Kotlin Coroutines
- **JSON**: Gson
- **Network**: OkHttp, Tor integration
- **Testing**: JUnit, Espresso, Mockito, Robolectric

## Target Platform
- **Min SDK**: API 26 (Android 8.0)
- **Target SDK**: API 34
- **Compile SDK**: API 35
- **Java Version**: 1.8
- **Kotlin Version**: 2.2.0