# BitChat Android - Dependencies & Frameworks

## Core Android Dependencies
- **androidx.core:core-ktx**: Android KTX extensions
- **androidx.activity:activity-compose**: Compose activity integration
- **androidx.appcompat:appcompat**: AppCompat support library

## UI Framework (Jetpack Compose)
- **androidx.compose:compose-bom**: Compose BOM for version alignment
- **androidx.compose.ui:ui**: Core Compose UI
- **androidx.compose.material3:material3**: Material Design 3
- **androidx.compose.material:material-icons-extended**: Extended icon set
- **androidx.navigation:navigation-compose**: Compose navigation

## Lifecycle & Architecture
- **androidx.lifecycle:lifecycle-runtime-ktx**: Lifecycle KTX
- **androidx.lifecycle:lifecycle-viewmodel-compose**: ViewModel Compose integration
- **androidx.lifecycle:lifecycle-livedata-ktx**: LiveData KTX

## Permissions & UI
- **com.google.accompanist:accompanist-permissions**: Permission handling

## Cryptography
- **org.bouncycastle:bcprov-jdk15on**: BouncyCastle crypto provider
- **com.google.crypto.tink:tink-android**: Google Tink crypto library

## Networking & Communication
- **no.nordicsemi.android:ble**: Nordic BLE library for Bluetooth LE
- **com.squareup.okhttp3:okhttp**: HTTP client for Nostr relays
- **info.guardianproject:arti-mobile-ex**: Tor integration (Rust-based)

## Data & Serialization
- **com.google.code.gson:gson**: JSON serialization
- **org.jetbrains.kotlinx:kotlinx-coroutines-android**: Coroutines for Android

## Location Services
- **com.google.android.gms:play-services-location**: Google Play Services Location

## Security & Storage
- **androidx.security:security-crypto**: Encrypted SharedPreferences

## Testing Framework
- **junit:junit**: Unit testing
- **androidx.test.ext:junit**: AndroidX test extensions
- **androidx.test.espresso:espresso-core**: UI testing
- **org.mockito.kotlin:mockito-kotlin**: Mocking framework
- **org.robolectric:robolectric**: Android unit testing

## Build Configuration
- **AGP**: 8.10.1 (Android Gradle Plugin)
- **Kotlin**: 2.2.0 with Compose support
- **Gradle JVM Args**: -Xmx4g -XX:MaxMetaspaceSize=512m
- **Parallel Builds**: Enabled (org.gradle.parallel=true)