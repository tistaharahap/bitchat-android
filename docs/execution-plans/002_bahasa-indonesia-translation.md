# Bahasa Indonesia Translation and Cultural Adaptation

**Plan ID**: 002  
**Phase**: Translation & Localization  
**Estimated Duration**: 4-6 days  
**Risk Level**: Medium  
**Dependencies**: Plan 001 (Infrastructure Setup)  

## Overview
Comprehensive translation of BitChat Android to Bahasa Indonesia with cultural adaptation for Indonesian users, maintaining IRC protocol compatibility while providing native language experience.

## Translation Strategy

### 1. Cultural Adaptation Framework

**Target Audience**: Indonesian mobile users familiar with chat applications  
**Language Standard**: Bahasa Indonesia Baku (Formal Indonesian)  
**Regional Considerations**: 
- Indonesia has 17,500+ islands with diverse local languages
- Standard Indonesian serves as lingua franca
- Tech-savvy users familiar with English technical terms
- Growing mesh networking and decentralized communication interest

**Cultural Adaptation Principles**:
- Use formal Indonesian for system messages and UI
- Maintain familiar chat terminology (channel, nickname, message)
- Adapt time formats to Indonesian standards (24-hour format)
- Consider Indonesian politeness levels in messaging

### 2. Translation Domains

#### 2.1 Core Application Strings (`strings.xml`)

**Current English → Bahasa Indonesia**:
```xml
<!-- App Identity -->
<string name="app_name">bitchat</string> <!-- KEEP: Brand name -->

<!-- Permissions -->
<string name="permission_bluetooth_rationale">
    "Bluetooth permission is required for peer-to-peer messaging without internet."
    → "Izin Bluetooth diperlukan untuk pesan antar-pengguna tanpa internet."
</string>

<string name="permission_location_rationale">
    "Location permission is required to discover nearby devices via Bluetooth."
    → "Izin lokasi diperlukan untuk menemukan perangkat terdekat melalui Bluetooth."
</string>

<string name="permission_notification_rationale">
    "Notification permission is required to alert you of new messages."
    → "Izin notifikasi diperlukan untuk memberi tahu Anda tentang pesan baru."
</string>

<!-- UI Elements -->
<string name="nickname_hint">nickname → nama panggilan</string>
<string name="message_hint">type a message… → ketik pesan…</string>
<string name="channel_password_hint">Password → Kata Sandi</string>
<string name="join_channel">Join Channel → Gabung Saluran</string>
<string name="leave_channel">Leave → Keluar</string>
<string name="send_message">Send → Kirim</string>
<string name="show_commands">Show commands → Tampilkan perintah</string>
<string name="back">Back → Kembali</string>
<string name="people">People → Orang</string>
<string name="channels">Channels → Saluran</string>
<string name="online_users">Online Users → Pengguna Online</string>
<string name="no_one_connected">No one connected → Tidak ada yang terhubung</string>
<string name="emergency_clear_hint">Triple tap to clear all data → Ketuk tiga kali untuk menghapus semua data</string>
<string name="your_network">Network → Jaringan</string>
```

#### 2.2 Command Descriptions (`strings_commands.xml`)

**IRC Commands Stay English, Descriptions Translated**:
```xml
<!-- Command descriptions for autocomplete -->
<string name="cmd_block_desc">blokir atau tampilkan daftar pengguna yang diblokir</string>
<string name="cmd_channels_desc">tampilkan semua saluran yang ditemukan</string>
<string name="cmd_clear_desc">hapus pesan obrolan</string>
<string name="cmd_hug_desc">kirim pelukan hangat ke seseorang</string>
<string name="cmd_join_desc">gabung atau buat saluran</string>
<string name="cmd_msg_desc">kirim pesan pribadi</string>
<string name="cmd_slap_desc">tampar seseorang dengan ikan trout</string>
<string name="cmd_unblock_desc">buka blokir pengguna</string>
<string name="cmd_who_desc">lihat siapa yang online</string>

<!-- Channel-specific commands -->
<string name="cmd_pass_desc">ubah kata sandi saluran</string>
<string name="cmd_save_desc">simpan pesan saluran secara lokal</string>
<string name="cmd_transfer_desc">transfer kepemilikan saluran</string>
```

#### 2.3 System Messages (`strings_system.xml`)

**System Response Messages**:
```xml
<!-- Join/Leave Messages -->
<string name="sys_joined_channel">bergabung dengan saluran %s</string>
<string name="sys_left_channel">meninggalkan saluran %s</string>
<string name="sys_channel_created">saluran %s dibuat</string>

<!-- Usage Instructions -->
<string name="sys_usage_join">penggunaan: /join &lt;saluran&gt;</string>
<string name="sys_usage_msg">penggunaan: /msg &lt;nama_panggilan&gt; [pesan]</string>
<string name="sys_usage_pass">penggunaan: /pass &lt;kata_sandi&gt;</string>
<string name="sys_usage_block">penggunaan: /block &lt;nama_panggilan&gt;</string>
<string name="sys_usage_unblock">penggunaan: /unblock &lt;nama_panggilan&gt;</string>
<string name="sys_usage_hug">penggunaan: /hug &lt;nama_panggilan&gt;</string>
<string name="sys_usage_slap">penggunaan: /slap &lt;nama_panggilan&gt;</string>

<!-- Private Chat Messages -->
<string name="sys_started_private_chat">memulai obrolan pribadi dengan %s</string>
<string name="sys_user_not_found">pengguna \'%s\' tidak ditemukan. mereka mungkin offline atau menggunakan nama panggilan yang berbeda.</string>
<string name="sys_user_blocked">pengguna %s telah diblokir</string>
<string name="sys_user_unblocked">pengguna %s telah dibuka blokirnya</string>

<!-- Channel Management -->
<string name="sys_password_changed">kata sandi diubah untuk saluran %s</string>
<string name="sys_not_in_channel">Anda harus berada di saluran untuk mengatur kata sandi.</string>
<string name="sys_not_channel_creator">Anda harus menjadi pembuat saluran untuk mengatur kata sandi.</string>

<!-- Who Command -->
<string name="sys_no_one_around">tidak ada orang lain di sekitar saat ini.</string>
<string name="sys_online_users">pengguna online: %s</string>
<string name="sys_participants_in_location">peserta di %s: %s</string>

<!-- Channel List -->
<string name="sys_no_channels_joined">tidak ada saluran yang diikuti</string>
<string name="sys_joined_channels">saluran yang diikuti: %s</string>

<!-- Unknown Command -->
<string name="sys_unknown_command">perintah tidak dikenal: %s. ketik / untuk melihat perintah yang tersedia.</string>

<!-- Action Messages (keep playful tone) -->
<string name="sys_action_hug_format">* %s memberi %s pelukan hangat 🫂 *</string>
<string name="sys_action_slap_format">* %s menampar %s dengan ikan trout besar 🐟 *</string>
```

#### 2.4 Error Messages (`strings_errors.xml`)

**Error and Warning Messages**:
```xml
<!-- Connection Errors -->
<string name="error_connection_failed">Koneksi gagal</string>
<string name="error_bluetooth_unavailable">Bluetooth tidak tersedia</string>
<string name="error_permission_denied">Izin ditolak</string>
<string name="error_location_unavailable">Lokasi tidak tersedia</string>

<!-- Message Errors -->
<string name="error_message_send_failed">Gagal mengirim pesan</string>
<string name="error_message_too_long">Pesan terlalu panjang</string>
<string name="error_invalid_command">Perintah tidak valid</string>

<!-- Channel Errors -->
<string name="error_channel_join_failed">Gagal bergabung dengan saluran</string>
<string name="error_invalid_channel_name">Nama saluran tidak valid</string>
<string name="error_channel_password_required">Kata sandi saluran diperlukan</string>
<string name="error_incorrect_password">Kata sandi salah</string>

<!-- User Errors -->
<string name="error_user_not_found">Pengguna tidak ditemukan</string>
<string name="error_cannot_message_blocked_user">Tidak dapat mengirim pesan ke pengguna yang diblokir</string>
<string name="error_cannot_message_self">Tidak dapat mengirim pesan ke diri sendiri</string>

<!-- Technical Errors -->
<string name="error_encryption_failed">Enkripsi gagal</string>
<string name="error_network_timeout">Waktu jaringan habis</string>
<string name="error_insufficient_storage">Penyimpanan tidak mencukupi</string>
```

#### 2.5 Notification Strings (`strings_notifications.xml`)

**Notification Content**:
```xml
<!-- Notification Channels -->
<string name="notification_channel_dm_name">Pesan Langsung</string>
<string name="notification_channel_dm_desc">Notifikasi untuk pesan pribadi dari pengguna lain</string>
<string name="notification_channel_geohash_name">Obrolan Lokasi</string>
<string name="notification_channel_geohash_desc">Notifikasi untuk pesan di saluran lokasi terdekat</string>

<!-- Message Notifications -->
<string name="notification_new_message">Pesan baru dari %s</string>
<string name="notification_new_messages">%d pesan baru dari %s</string>
<string name="notification_multiple_senders">Pesan dari %d pengguna</string>

<!-- Geohash Notifications -->
<string name="notification_geohash_message">Pesan di %s</string>
<string name="notification_geohash_mention">%s menyebut Anda di %s</string>
<string name="notification_geohash_first_message">Pesan pertama di lokasi Anda</string>

<!-- System Notifications -->
<string name="notification_mesh_connected">Terhubung ke jaringan mesh</string>
<string name="notification_mesh_disconnected">Terputus dari jaringan mesh</string>
<string name="notification_active_peers">%d pengguna aktif terdekat</string>
```

### 3. Technical Implementation

#### 3.1 String Resource Creation Process

**Day 1-2: Extract and Categorize**
```bash
# 1. Audit all hardcoded strings in key files
grep -r "\"[^\"]*\"" app/src/main/java/com/bitchat/android/ui/ > hardcoded_strings.txt

# 2. Categorize by domain:
# - Commands: CommandProcessor.kt
# - System: All managers
# - Errors: Exception handling
# - Notifications: NotificationManager.kt
# - UI: Compose components
```

**Day 2-3: Create Resource Files**
1. Create base English resource files with extracted strings
2. Generate string resource IDs following Android conventions
3. Replace hardcoded strings with resource references

**Day 3-4: Indonesian Translation**
1. Professional translation of all string resources
2. Cultural adaptation review by Indonesian native speaker
3. Technical terminology validation
4. UI length testing for Indonesian text

#### 3.2 Cultural Adaptation Guidelines

**Formality Level**: Use formal Indonesian (tidak/bukan vs tak/nggak)
**Technical Terms**: 
- "channel" → "saluran" (established Indonesian translation)
- "nickname" → "nama panggilan" (standard term)
- "message" → "pesan" (universal)
- "user" → "pengguna" (formal)
- "online" → "online" (borrowed term, widely used)

**Time/Date Format Adaptations**:
```kotlin
// Indonesian locale considerations
DateFormat.getDateTimeInstance(DateFormat.MEDIUM, DateFormat.SHORT, Locale("id", "ID"))
// Example: "31 Agu 2025, 14:30" instead of "Aug 31, 2025, 2:30 PM"
```

**UI Text Length Considerations**:
- Indonesian text often 20-30% longer than English
- Button labels may need resizing
- Error messages may require multiline layout
- Consider abbreviations for space-constrained UI elements

#### 3.3 Indonesian-Specific String Implementations

**Pluralization Handling**:
```xml
<!-- Indonesian doesn't use plural forms like English -->
<!-- English: "1 message" vs "2 messages" -->
<!-- Indonesian: "1 pesan" vs "2 pesan" (same form) -->

<string name="message_count_format">%d pesan</string>
<string name="user_count_format">%d pengguna</string>
<string name="hour_format">%d jam</string>
<string name="minute_format">%d menit</string>
```

**Politeness and Tone**:
```xml
<!-- System messages should be polite but not overly formal -->
<string name="sys_welcome_message">Selamat datang di BitChat! Temukan dan hubungi pengguna terdekat tanpa internet.</string>
<string name="sys_connection_established">Koneksi berhasil dibuat dengan %s</string>
<string name="sys_help_available">Ketik / untuk melihat daftar perintah yang tersedia</string>
```

### 4. Implementation Timeline

#### Day 1: String Extraction and Cataloguing
**Morning (4 hours)**:
- Audit `CommandProcessor.kt` for all hardcoded strings (estimated 50+ strings)
- Audit `NotificationManager.kt` for notification strings (estimated 20+ strings)
- Audit `PrivateChatManager.kt` for system messages (estimated 15+ strings)

**Afternoon (4 hours)**:
- Audit onboarding screens for permission explanations (estimated 25+ strings)
- Audit UI components for labels and hints (estimated 30+ strings)
- Create comprehensive string inventory with categorization

#### Day 2: Resource File Creation
**Morning (4 hours)**:
- Create structured English resource files:
  - `strings_commands.xml` (IRC command descriptions)
  - `strings_system.xml` (system messages and responses)
  - `strings_errors.xml` (error and warning messages)
  - `strings_notifications.xml` (notification content)

**Afternoon (4 hours)**:
- Update existing `strings.xml` with additional UI strings
- Generate string resource IDs following Android naming conventions
- Create string resource reference mapping document

#### Day 3: Indonesian Translation
**Morning (4 hours)**:
- Professional translation of core UI strings (40+ strings)
- Translation of command descriptions maintaining technical accuracy
- Cultural adaptation review of system messages

**Afternoon (4 hours)**:
- Translation of error messages and notifications
- Technical terminology validation with Indonesian tech community
- Pluralization and formatting adaptation for Indonesian language rules

#### Day 4: Cultural Adaptation and Review
**Morning (4 hours)**:
- Indonesian native speaker review of all translations
- Cultural sensitivity assessment for system messages
- Politeness level adjustment for formal Indonesian standards
- Technical accuracy validation

**Afternoon (4 hours)**:
- UI length testing with Indonesian strings
- Layout adjustment recommendations for longer text
- Button and label sizing validation
- Overflow handling for extended Indonesian text

#### Day 5: Implementation Integration
**Morning (4 hours)**:
- Replace hardcoded strings in `CommandProcessor.kt` with resource references
- Update system message generation in all UI managers
- Implement StringResourceProvider integration

**Afternoon (4 hours)**:
- Update notification generation with Indonesian strings
- Test string resolution in Indonesian locale
- Validate formatting with Indonesian parameters

#### Day 6: Testing and Quality Assurance
**Morning (4 hours)**:
- Comprehensive testing in Indonesian locale
- IRC command compatibility validation
- System message accuracy testing
- Notification display testing

**Afternoon (4 hours)**:
- Edge case testing (long usernames, long channel names)
- Performance testing with Indonesian string resources
- Memory usage validation with additional string resources
- Final quality assurance review

### 5. Cultural Context Examples

#### 5.1 Indonesian Chat Culture Adaptations

**Greeting Patterns**:
```xml
<!-- Indonesians often use more formal greetings -->
<string name="sys_user_joined">%s bergabung dengan obrolan</string>
<string name="sys_user_left">%s meninggalkan obrolan</string>
<string name="sys_welcome_to_channel">Selamat datang di saluran %s</string>
```

**Error Messages (Polite but Clear)**:
```xml
<!-- Indonesian error messages tend to be more apologetic -->
<string name="error_connection_lost">Maaf, koneksi terputus. Mencoba menghubungkan kembali...</string>
<string name="error_permission_needed">Aplikasi memerlukan izin ini untuk berfungsi dengan baik</string>
<string name="error_please_try_again">Silakan coba lagi dalam beberapa saat</string>
```

#### 5.2 Technical Terminology Standards

**Established Indonesian Tech Terms**:
- "aplikasi" (application)
- "pengguna" (user, formal) vs "user" (informal, widely used)
- "saluran" (channel)
- "pesan" (message)
- "notifikasi" (notification)
- "koneksi" (connection)
- "jaringan" (network)
- "enkripsi" (encryption)

**Borrowed Terms (Keep in English)**:
- "Bluetooth" (universal term)
- "online" (widely used in Indonesian)
- "offline" (widely used in Indonesian)
- "mesh" (technical term, no direct translation)
- Brand names: "BitChat", "Android", "iOS"

### 6. Quality Assurance Framework

#### 6.1 Translation Validation Checklist

**Linguistic Quality**:
- ✅ Formal Indonesian grammar and spelling
- ✅ Consistent terminology throughout app
- ✅ Natural Indonesian sentence structure
- ✅ Appropriate politeness level for system messages
- ✅ Technical accuracy maintained in translation

**Cultural Appropriateness**:
- ✅ Respectful tone in error messages
- ✅ Indonesian social conventions observed
- ✅ No culturally sensitive content
- ✅ Appropriate formality level for tech application

**Technical Accuracy**:
- ✅ IRC commands remain in English
- ✅ Protocol messages unchanged
- ✅ Technical terms consistently translated
- ✅ String formatting works with Indonesian text
- ✅ No breaking of app functionality

#### 6.2 Testing Strategy

**Device Testing**:
```kotlin
// Test on devices with Indonesian locale
Locale.setDefault(Locale("id", "ID"))

// Test scenarios:
1. Set device to Indonesian language
2. Verify all UI text displays in Indonesian
3. Test IRC commands still work in English
4. Verify system messages appear in Indonesian
5. Test notification content in Indonesian
6. Validate error messages in Indonesian
```

**Compatibility Testing**:
- Test BitChat Android (Indonesian) ↔ BitChat iOS (English) communication
- Verify channel names and usernames display correctly across platforms
- Test private messaging between Indonesian and English clients
- Validate protocol-level compatibility maintained

### 7. Files to Create/Modify

#### New Indonesian Resource Files:
1. `/app/src/main/res/values-id/strings.xml`
2. `/app/src/main/res/values-id/strings_commands.xml`
3. `/app/src/main/res/values-id/strings_system.xml`
4. `/app/src/main/res/values-id/strings_errors.xml`
5. `/app/src/main/res/values-id/strings_notifications.xml`

#### English Resource Restructuring:
1. `/app/src/main/res/values/strings_commands.xml` (new)
2. `/app/src/main/res/values/strings_system.xml` (new)
3. `/app/src/main/res/values/strings_errors.xml` (new)
4. `/app/src/main/res/values/strings_notifications.xml` (new)
5. `/app/src/main/res/values/strings.xml` (updated)

#### Code Files Requiring String Resource Integration:
1. `/app/src/main/java/com/bitchat/android/ui/CommandProcessor.kt`
2. `/app/src/main/java/com/bitchat/android/ui/NotificationManager.kt`
3. `/app/src/main/java/com/bitchat/android/ui/PrivateChatManager.kt`
4. `/app/src/main/java/com/bitchat/android/ui/ChannelManager.kt`
5. `/app/src/main/java/com/bitchat/android/ui/MessageManager.kt`
6. All onboarding screen components
7. All UI Compose components with hardcoded text

## Success Criteria

### Translation Quality:
- ✅ 100% of user-facing strings translated to natural Indonesian
- ✅ IRC commands remain in English for iOS compatibility  
- ✅ Cultural adaptation reviewed by Indonesian native speaker
- ✅ Technical terminology consistent throughout application
- ✅ UI layout accommodates longer Indonesian text without overflow

### Protocol Compatibility:
- ✅ IRC commands function identically in both English and Indonesian versions
- ✅ Channel names, usernames, and protocol messages maintain compatibility
- ✅ Cross-platform communication works flawlessly (Android ID ↔ iOS EN)
- ✅ No breaking changes to mesh networking protocol

### User Experience:
- ✅ Natural, intuitive Indonesian interface
- ✅ Appropriate formality level for Indonesian users
- ✅ Error messages helpful and culturally appropriate
- ✅ Notifications clear and actionable in Indonesian
- ✅ Help text and command descriptions easily understood

## Risk Mitigation

### Translation Risks:
- **Risk**: Poor translation quality affecting user experience
- **Mitigation**: Professional translation + native speaker review + community feedback

### Technical Risks:
- **Risk**: Indonesian strings breaking UI layout due to length differences
- **Mitigation**: UI testing with Indonesian strings + layout flexibility testing

### Compatibility Risks:
- **Risk**: Translation affecting iOS protocol compatibility
- **Mitigation**: Keep all protocol-level communication in English + extensive compatibility testing

### Cultural Risks:
- **Risk**: Inappropriate tone or cultural insensitivity in system messages
- **Mitigation**: Cultural adaptation review + Indonesian community feedback + iterative refinement

This comprehensive translation plan ensures high-quality localization while maintaining technical compatibility and cultural appropriateness for Indonesian users.