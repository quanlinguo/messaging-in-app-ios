# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository contains **example applications** that demonstrate the Salesforce Messaging for In-App iOS SDK. The actual SDK source code is distributed via Swift Package Manager from a separate repository (`Swift-Package-InAppMessaging`).

## Project Structure

The repository contains two example apps within `MessagingExample.xcworkspace`:

- **MessagingCoreExample** - Demonstrates the low-level Core SDK (`SMIClientCore`) with a custom UI
- **MessagingUIExample** - Demonstrates the high-level UI SDK (`SMIClientUI`) with pre-built interface
- **Shared/** - Extensive shared code (~81 files, 4500+ lines) used by both examples

## Building and Running

### Opening the Project

```bash
# Open the workspace (contains both example projects)
open MessagingExample.xcworkspace
```

### Building from Command Line

Note: Requires full Xcode installation (not just Command Line Tools).

```bash
# Build Core SDK example
xcodebuild -workspace MessagingExample.xcworkspace \
  -scheme MessagingCoreExample \
  -sdk iphonesimulator \
  build

# Build UI SDK example
xcodebuild -workspace MessagingExample.xcworkspace \
  -scheme MessagingUIExample \
  -sdk iphonesimulator \
  build

# Run on simulator
xcodebuild -workspace MessagingExample.xcworkspace \
  -scheme MessagingUIExample \
  -sdk iphonesimulator \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  run
```

### Configuration Requirements

Before running the examples, you must configure Salesforce credentials:

1. **For MessagingCoreExample**: Edit `examples/MessagingCoreExample/configFile.json`
2. **For MessagingUIExample**: Configure via in-app Settings UI (persisted to UserDefaults)

Required values:
- `OrganizationId` - Salesforce org ID
- `DeveloperName` - Messaging deployment developer name
- `Url` - Salesforce SCRT URL (format: `https://<org>.salesforce-scrt.com`)

## SDK Architecture

### Two-Layer Architecture

The SDK follows a layered design:

1. **SMIClientCore** (Lower Layer)
   - Headless messaging functionality
   - Network communication via Server-Sent Events (SSE)
   - Core data models and protocols
   - No UI dependencies
   - Entry point: `CoreFactory.create(withConfig:)` → `CoreClient`

2. **SMIClientUI** (Upper Layer)
   - Built on top of SMIClientCore
   - Pre-built SwiftUI messaging interface
   - Customizable UI components
   - Entry point: `Interface(config, preChatFieldValueProvider:, chatFeedViewBuilder:, navigationBarBuilder:)`

### Key Entry Points

#### Core SDK Workflow
```swift
// 1. Create configuration
let config = Configuration(fromJson: data)

// 2. Create core client
let coreClient = CoreFactory.create(withConfig: config)

// 3. Start SSE connection
coreClient.start()

// 4. Register delegates (optional)
coreClient.setPreChatDelegate(delegate: self, queue: .main)
coreClient.setTemplatedUrlDelegate(delegate: self, queue: .main)
coreClient.setUserVerificationDelegate(delegate: self, queue: .main)

// 5. Create conversation client
let conversationClient = coreClient.conversationClient(with: conversationID)

// 6. Retrieve existing messages
conversationClient.entries(withLimit: 100, fromTimestamp: nil,
                          direction: .older, behaviour: .new)

// 7. Send messages
conversationClient.send(message: "Hello")
conversationClient.send(image: imageData, fileName: "photo.jpg")
conversationClient.send(reply: choice)
```

#### UI SDK Workflow
```swift
// 1. Create UI configuration
let uiConfig = UIConfiguration(
    configuration: config,
    conversationId: conversationID,
    remoteLocaleMap: nil,
    urlDisplayMode: .embedded
)

// 2. Configure optional features
uiConfig.conversationOptionsConfiguration = ConversationOptionsConfiguration(allowEndChat: true)
uiConfig.transcriptConfiguration = TranscriptConfiguration(allowTranscriptDownload: true)
uiConfig.attachmentConfiguration = AttachmentConfiguration(endUserToAgent: true)

// 3. Create interface with optional customization
Interface(uiConfig,
          preChatFieldValueProvider: preChatClosure,
          chatFeedViewBuilder: customViewBuilder,
          navigationBarBuilder: customNavBarBuilder)
```

### Delegate Pattern

The SDK extensively uses delegates for extensibility:

**Core SDK Delegates:**
- `CoreDelegate` - Global events (entries received, typing indicators, network state, errors)
- `ConversationClientDelegate` - Per-conversation events (participant updates)
- `HiddenPreChatDelegate` - Async pre-chat field population
- `TemplatedUrlDelegate` - Template value substitution in auto-responses
- `UserVerificationDelegate` - JWT token provisioning for authenticated sessions

**UI SDK Delegates:**
- `ChatFeedViewBuilder` - Custom UI replacement strategy
- `NavigationBarBuilder` - Dynamic navigation bar customization

**Example Implementations:**
- `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler.swift` - Central delegate coordinator
- `examples/MessagingCoreExample/Delegates/MessagingViewModel+CoreDelegate.swift` - CoreDelegate implementation

### UI Customization Pattern

The UI SDK uses a render mode strategy for selective UI replacement:

```swift
// ChatFeedViewBuilder protocol
protocol ChatFeedViewBuilder {
    var renderMode: RenderModeClosure? { get }
    var completeView: CompleteViewClosure? { get }
}

// RenderMode decisions
enum RenderMode {
    case existing  // Use SDK's default UI
    case replace   // Use custom view
    case none      // Hide this entry
}

// Example: Replace only specific message types
let builder = TestEntryViewBuilder()
builder.renderMode = { model, client in
    if model is ConversationEntryTextMessageModel {
        return .replace  // Show custom UI for text messages
    }
    return .existing  // Use SDK UI for everything else
}
```

Located in: `examples/Shared/Delegates/Providers/TestEntryViewBuilder.swift`

### Settings Store Pattern

The shared code uses a type-safe settings framework:

```swift
// Generic UserDefaults-backed storage
class SettingsStore<Keys: Settings>: ObservableObject

// Example stores:
- MIAWConfigurationStore - SDK configuration
- DelegateManagementStore - Delegate enable/disable flags
- UIReplacementStore - UI customization settings
- ConversationManagementStore - Conversation UUID persistence
```

Features:
- Type-safe property wrappers
- Version migration (clears settings on schema changes)
- Observable for SwiftUI integration
- Persisted across app launches

Location: `examples/Shared/Settings/Storage/SettingsStore.swift`

### Conversation Entry Types

The SDK defines multiple entry types, all conforming to `ConversationEntry`:

- `TextMessage` - Text messages from user/agent/bot
- `Attachment` - Image/PDF attachments
- `Choice` - Quick reply buttons
- `ListPicker` - Selection menus
- `BusinessHours` - Business hours messaging
- `TypingIndicator` - Typing status
- `ParticipantChanged` - Agent join/leave events

Each entry has:
- `id: UUID` - Unique identifier
- `payload` - Type-specific data
- `timestamp: Date` - Message timestamp
- `sender: Participant` - Message sender info

### Conversation Client Methods

Key methods available on `ConversationClient`:

```swift
// Message sending
send(message: String)
send(image: Data, fileName: String)
send(pdf: PDFDocument)
send(reply: Choice)

// Message retrieval
entries(withLimit: Int, fromTimestamp: Date?,
        direction: Direction, behaviour: Behaviour)

// Pre-chat submission
submit(remoteConfig: RemoteConfiguration,
       createConversationOnSubmit: Bool)

// Transcript download
retrieveTranscript(completion: (PDFDocument?, Error?) -> Void)

// Delegate management
addDelegate(delegate: ConversationClientDelegate, queue: DispatchQueue)
```

### Recent Features (v1.9.0+)

**MessagingSessionWidget** - Floating button with session information:
- `queuePositionView()` - Display queue position
- `sessionStatusView()` - Show connection status
- `agentNameView()` - Show agent name with unread counter

Location: `examples/MessagingUIExample/Views/MessagingSessionWidget.swift`

## Important Implementation Notes

### Conversation ID Persistence

For conversation continuity across app restarts, **always persist and reuse the conversation UUID**:

```swift
// BAD: New UUID each time = new conversation
let conversationID = UUID()

// GOOD: Persisted UUID = continuous conversation
let conversationID = UserDefaults.standard.getConversationUUID() ?? UUID()
UserDefaults.standard.saveConversationUUID(conversationID)
```

Example: `examples/Shared/Settings/Models/ConversationManagement.swift`

### Delegate Registration Timing

Register delegates **before** creating conversation client:

```swift
// 1. Create core client
let coreClient = CoreFactory.create(withConfig: config)

// 2. Register delegates FIRST
coreClient.setUserVerificationDelegate(delegate: self, queue: .main)

// 3. Then create conversation client
let conversationClient = coreClient.conversationClient(with: uuid)
```

### Message Entry Retrieval

When fetching conversation history, note the `direction` parameter behavior:

```swift
// Fetch older messages (most common - chat history)
entries(withLimit: 100, fromTimestamp: oldestMessageTimestamp,
        direction: .older, behaviour: .new)

// Fetch newer messages (rare - refresh after disconnect)
entries(withLimit: 100, fromTimestamp: newestMessageTimestamp,
        direction: .newer, behaviour: .new)
```

### GlobalCoreDelegateHandler Pattern

Both examples use a shared singleton pattern for delegate management:

```swift
// Singleton coordinator
class GlobalCoreDelegateHandler: NSObject {
    static let shared = GlobalCoreDelegateHandler()

    // Delegate implementations
    let viewBuilder: TestEntryViewBuilder
    let navBarBuilder: TestNavBarBuilder
    let prePopulatedPreChatProvider: TestPrePopulatedPreChatProvider
    // ... other delegates

    func registerDelegates(_ core: CoreClient?)
    func registerDelegates(_ client: ConversationClient)
}
```

This centralizes delegate configuration and allows easy enable/disable via `DelegateManagementStore`.

Location: `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler.swift`

## Testing

This repository contains no test suite. The actual SDK repository contains tests.

For manual testing:
1. Run example apps in simulator or on device
2. Configure with valid Salesforce org credentials
3. Test messaging flows via Settings UI configurations

## Authentication Methods

The SDK supports three authentication approaches:

1. **Unverified** - No authentication (fastest setup)
2. **User Verified (JWT)** - Customer provides JWT via `UserVerificationDelegate`
3. **Passthrough** - Integrates with Salesforce Mobile SDK authentication

Configure in `Configuration.userVerificationRequired` flag.

## Customization Examples

The examples demonstrate extensive customization:

**String Customization:**
- Edit `examples/MessagingUIExample/Localizable.strings`
- Override SDK strings with custom translations

**Color Customization:**
- Edit `examples/MessagingUIExample/Assets.xcassets`
- Override SDK color assets with custom colors

**UI Replacement:**
- See `examples/Shared/ReplacementUI/` for custom view implementations
- Each conversation entry type has a custom view model and SwiftUI view
- 12 different entry types with custom replacements provided

**Pre-Chat Field Auto-Population:**
- Implement `HiddenPreChatDelegate`
- Return values for specific field IDs
- See `examples/Shared/Delegates/Providers/TestPrePopulatedPreChatProvider.swift`

## File Locations Reference

Key architectural files to understand:

- **Core SDK initialization**: `examples/MessagingCoreExample/Models/MessagingViewModel.swift:34`
- **UI SDK initialization**: `examples/MessagingUIExample/Views/ContentView.swift:48-60`
- **Delegate coordinator**: `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler.swift:13-40`
- **Settings framework**: `examples/Shared/Settings/Storage/SettingsStore.swift:8-50`
- **UI replacement strategy**: `examples/Shared/Delegates/Providers/TestEntryViewBuilder.swift`
- **Custom entry views**: `examples/Shared/ReplacementUI/Views/ConversationEntry/`
- **Configuration models**: `examples/Shared/Settings/Models/`

## Swift Package Dependencies

Both examples reference these SPM packages:

- **SMIClientCore** - Core messaging SDK
- **SMIClientUI** - UI messaging SDK
- **SalesforceMobileSDK-iOS-SPM** - Optional, for passthrough auth example
  - MobileSync
  - SalesforceAnalytics
  - SalesforceSDKCore

Repository: `https://github.com/Salesforce-Async-Messaging/Swift-Package-InAppMessaging.git`

## Common Patterns

### SwiftUI + Combine

The codebase is SwiftUI-first with extensive use of Combine:

```swift
@Published var conversationEntries: [ConversationEntry]
@StateObject var configStore: MIAWConfigurationStore
@ObservedObject var viewModel: MessagingViewModel
```

### Protocol-Oriented Design

Heavy use of protocols for flexibility:

```swift
protocol Settings { /* Type-safe settings keys */ }
protocol ChatFeedViewBuilder { /* UI customization */ }
protocol ConversationClientDelegate { /* Event handling */ }
```

### Configuration-Driven

Prefer configuration objects over numerous method parameters:

```swift
// UIConfiguration with nested configs
config.conversationOptionsConfiguration = ConversationOptionsConfiguration(...)
config.transcriptConfiguration = TranscriptConfiguration(...)
config.attachmentConfiguration = AttachmentConfiguration(...)
```

## Documentation Links

- [Messaging for In-App Developer Guide](https://developer.salesforce.com/docs/service/messaging-in-app/overview)
- [iOS Reference Documentation](https://salesforce-async-messaging.github.io/messaging-in-app-ios/)
- [iOS Release Notes](https://github.com/Salesforce-Async-Messaging/messaging-in-app-ios/releases)
- [Android SDK Repository](https://github.com/Salesforce-Async-Messaging/messaging-in-app-android)
