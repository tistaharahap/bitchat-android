# Infrastructure Setup for BitChat Android Internationalization

**Plan ID**: 001  
**Phase**: Infrastructure  
**Estimated Duration**: 3-5 days  
**Risk Level**: Medium  
**Dependencies**: None  

## Overview
Establish the foundational infrastructure for internationalization (i18n) of BitChat Android, creating a robust system for supporting Bahasa Indonesia and future language additions while maintaining IRC command compatibility with iOS.

## Technical Architecture

### 1. String Resource Migration Strategy

**Current State Analysis**:
- `/app/src/main/res/values/strings.xml` contains ~40 base strings
- `CommandProcessor.kt` has ~50 hardcoded command descriptions and system messages
- `NotificationManager.kt` contains ~20 hardcoded notification strings
- `PermissionManager.kt` has ~15 hardcoded permission explanations
- Multiple UI managers contain scattered hardcoded strings

**Target Architecture**:
```
app/src/main/res/
├── values/                     # Default (English)
│   ├── strings.xml            # Core app strings
│   ├── strings_commands.xml   # IRC command descriptions
│   ├── strings_system.xml     # System messages
│   ├── strings_errors.xml     # Error messages
│   └── strings_notifications.xml # Notification content
├── values-id/                 # Indonesian (Bahasa Indonesia)
│   ├── strings.xml
│   ├── strings_commands.xml
│   ├── strings_system.xml
│   ├── strings_errors.xml
│   └── strings_notifications.xml
└── values-en/                 # Explicit English (future)
    └── [mirror of values/]
```

### 2. Context Injection Infrastructure

**Problem**: UI managers (CommandProcessor, NotificationManager, PrivateChatManager) lack Context access for string resources.

**Solution**: Implement dependency injection pattern for Context access:

```kotlin
// New: StringResourceProvider interface
interface StringResourceProvider {
    fun getString(@StringRes resId: Int): String
    fun getString(@StringRes resId: Int, vararg formatArgs: Any): String
}

// Implementation
class AndroidStringResourceProvider(private val context: Context) : StringResourceProvider {
    override fun getString(resId: Int): String = context.getString(resId)
    override fun getString(resId: Int, vararg formatArgs: Any): String = 
        context.getString(resId, *formatArgs)
}
```

### 3. IRC Command Compatibility Layer

**Requirement**: Keep IRC commands (/join, /msg, /who etc.) in English for iOS compatibility while translating descriptions and system responses.

**Implementation Strategy**:
```kotlin
data class CommandDefinition(
    val command: String,           // Always English: "/join"
    val aliases: List<String>,     // Always English: ["/j"]
    val descriptionKey: Int,       // Translatable: R.string.cmd_join_desc
    val usageKey: Int,            // Translatable: R.string.cmd_join_usage
    val paramPattern: String       // Always English: "<channel>"
)
```

## Implementation Steps

### Step 1: Create String Resource Files (Day 1)

**1.1 Extract Command Strings**
```bash
# Target file: app/src/main/res/values/strings_commands.xml
```

**String Extractions from CommandProcessor.kt**:
- Line 19: `"block or list blocked peers"` → `R.string.cmd_block_desc`
- Line 20: `"show all discovered channels"` → `R.string.cmd_channels_desc`
- Line 21: `"clear chat messages"` → `R.string.cmd_clear_desc`
- Line 22: `"send someone a warm hug"` → `R.string.cmd_hug_desc`
- Line 23: `"join or create a channel"` → `R.string.cmd_join_desc`
- Line 24: `"send private message"` → `R.string.cmd_msg_desc`
- Line 25: `"slap someone with a trout"` → `R.string.cmd_slap_desc`
- Line 26: `"unblock a peer"` → `R.string.cmd_unblock_desc`
- Line 27: `"see who's online"` → `R.string.cmd_who_desc`

**1.2 Extract System Message Strings**
```bash
# Target file: app/src/main/res/values/strings_system.xml
```

**System Messages from CommandProcessor.kt**:
- Line 64: `"joined channel $channel"` → `R.string.sys_joined_channel`
- Line 73: `"usage: /join <channel>"` → `R.string.sys_usage_join`
- Line 106: `"started private chat with $targetName"` → `R.string.sys_started_private_chat`
- Line 116: `"user '$targetName' not found..."` → `R.string.sys_user_not_found`
- Line 125: `"usage: /msg <nickname> [message]"` → `R.string.sys_usage_msg`
- Line 177: `"no one else is around right now."` → `R.string.sys_no_one_around`
- Line 179: `"$contextDescription: $peerList"` → `R.string.sys_user_list_format`

**1.3 Extract Error and Notification Strings**
```bash
# Target files: 
# - app/src/main/res/values/strings_errors.xml
# - app/src/main/res/values/strings_notifications.xml
```

### Step 2: Create StringResourceProvider Infrastructure (Day 1-2)

**2.1 Create Interface and Implementation**
```kotlin
// File: app/src/main/java/com/bitchat/android/i18n/StringResourceProvider.kt
interface StringResourceProvider {
    fun getString(@StringRes resId: Int): String
    fun getString(@StringRes resId: Int, vararg formatArgs: Any): String
}

class AndroidStringResourceProvider(private val context: Context) : StringResourceProvider {
    override fun getString(resId: Int): String = context.getString(resId)
    override fun getString(resId: Int, vararg formatArgs: Any): String = 
        context.getString(resId, *formatArgs)
}
```

**2.2 Modify Manager Constructors**
Add StringResourceProvider parameter to:
- `CommandProcessor` constructor
- `NotificationManager` constructor  
- `PrivateChatManager` constructor
- `ChannelManager` constructor
- Other UI managers with hardcoded strings

### Step 3: Update CommandProcessor Implementation (Day 2)

**3.1 Add StringResourceProvider to CommandProcessor**
```kotlin
class CommandProcessor(
    private val state: ChatState,
    private val messageManager: MessageManager,
    private val channelManager: ChannelManager,
    private val privateChatManager: PrivateChatManager,
    private val stringProvider: StringResourceProvider  // NEW
) {
    
    // Replace hardcoded descriptions with resource references
    private val baseCommands = listOf(
        CommandSuggestion("/block", emptyList(), "[nickname]", 
            stringProvider.getString(R.string.cmd_block_desc)),
        CommandSuggestion("/channels", emptyList(), null, 
            stringProvider.getString(R.string.cmd_channels_desc)),
        // ... continue for all commands
    )
}
```

**3.2 Replace Hardcoded System Messages**
```kotlin
// Before (Line 64):
content = "joined channel $channel"

// After:
content = stringProvider.getString(R.string.sys_joined_channel, channel)
```

### Step 4: Update Application Wiring (Day 2-3)

**4.1 Modify MainActivity and ViewModels**
Update dependency injection to pass StringResourceProvider to all managers:

```kotlin
// In MainActivity.kt or wherever managers are instantiated
private val stringProvider = AndroidStringResourceProvider(this)

private val commandProcessor = CommandProcessor(
    state, messageManager, channelManager, privateChatManager, stringProvider
)
```

**4.2 Update All Manager Instantiations**
Ensure all UI managers receive StringResourceProvider instance.

### Step 5: Create Indonesian String Resources (Day 3-4)

**5.1 Create Indonesian Resource Directory**
```bash
mkdir -p app/src/main/res/values-id
```

**5.2 Create Base strings.xml for Indonesian**
Copy and translate all string files to Indonesian equivalents.

**5.3 Validate Resource Loading**
Test that Indonesian strings load correctly on Indonesian locale devices.

### Step 6: Testing and Validation (Day 4-5)

**6.1 Unit Testing**
- Test StringResourceProvider functionality
- Verify correct string resolution in different locales
- Test command processing with translated strings

**6.2 Integration Testing**
- Test full app flow in Indonesian locale
- Verify IRC commands remain in English
- Test system message translations
- Validate notification translations

**6.3 Compatibility Testing**
- Test with iOS BitChat app interaction
- Verify protocol compatibility maintained
- Test command compatibility across platforms

## Files to Modify

### New Files to Create:
1. `/app/src/main/java/com/bitchat/android/i18n/StringResourceProvider.kt`
2. `/app/src/main/res/values/strings_commands.xml`
3. `/app/src/main/res/values/strings_system.xml`
4. `/app/src/main/res/values/strings_errors.xml`
5. `/app/src/main/res/values/strings_notifications.xml`
6. `/app/src/main/res/values-id/strings.xml`
7. `/app/src/main/res/values-id/strings_commands.xml`
8. `/app/src/main/res/values-id/strings_system.xml`
9. `/app/src/main/res/values-id/strings_errors.xml`
10. `/app/src/main/res/values-id/strings_notifications.xml`

### Existing Files to Modify:
1. `/app/src/main/java/com/bitchat/android/ui/CommandProcessor.kt`
2. `/app/src/main/java/com/bitchat/android/ui/NotificationManager.kt`
3. `/app/src/main/java/com/bitchat/android/ui/PrivateChatManager.kt`
4. `/app/src/main/java/com/bitchat/android/ui/ChannelManager.kt`
5. `/app/src/main/java/com/bitchat/android/MainActivity.kt`
6. `/app/src/main/java/com/bitchat/android/MainViewModel.kt`
7. All UI manager instantiation points

## Success Criteria

### Technical Criteria:
- ✅ All hardcoded strings extracted to resources
- ✅ StringResourceProvider successfully injected into all managers
- ✅ Indonesian strings load correctly in Indonesian locale
- ✅ IRC commands remain English for iOS compatibility
- ✅ Zero compilation errors or warnings
- ✅ All existing functionality preserved

### Quality Gates:
- ✅ Unit tests pass for string resource functionality
- ✅ Integration tests pass for full app flow in both locales
- ✅ Protocol compatibility maintained with iOS BitChat
- ✅ Performance impact <5ms per string resolution
- ✅ Memory usage increase <500KB for string resources

## Risk Mitigation

### High-Risk Areas:
1. **Context Access in Managers**: Some managers may not have direct Context access
   - *Mitigation*: Use dependency injection pattern with StringResourceProvider
   
2. **String Formatting Complexity**: Indonesian may require different formatting patterns
   - *Mitigation*: Use Android's built-in string formatting with placeholders
   
3. **Protocol Breaking Changes**: Incorrect translation might break iOS compatibility
   - *Mitigation*: Keep all IRC commands and protocol-level strings in English

### Rollback Strategy:
- Keep original hardcoded strings as fallbacks in StringResourceProvider
- Use feature flags to toggle between hardcoded and resource-based strings
- Maintain Git branches for easy rollback if issues arise

## Implementation Notes

### Command Processing Specifics:
- IRC commands (`/join`, `/msg`, `/who`, etc.) must remain in English
- Command descriptions and help text should be translated
- System responses to commands should be translated
- Error messages and usage instructions should be translated

### Cultural Considerations for Indonesian:
- Use formal Indonesian (Bahasa Indonesia Baku) for system messages
- Consider Indonesian naming conventions for UI elements
- Adapt time/date formatting for Indonesian locale
- Use appropriate Indonesian technical terminology

### Performance Considerations:
- String resource access adds ~1-2ms overhead per call
- Indonesian strings may be longer than English, affecting UI layout
- Resource loading impact during app startup should be minimal
- Consider string caching for frequently accessed messages

This infrastructure plan establishes the foundation for full internationalization while maintaining protocol compatibility and preparing for future language additions.