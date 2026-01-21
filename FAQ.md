# Frequently Asked Questions

## Which sample app should I use for handling intermittent network connectivity?

**Question:** I have a mobile publisher app for advanced messaging. Users experience intermittent network connectivity issues. When connectivity is lost, active sessions are dropped, leading to a poor user experience. Even when connectivity is restored, user sessions are lost. Salesforce suggested building a native app using the provided sample code to address these connectivity issues. Which sample app is most appropriate?

**Answer:** Use **MessagingUIExample** as your starting point.

### Why MessagingUIExample is Better for Network Reliability

#### 1. Built-in Conversation Persistence

MessagingUIExample implements conversation UUID persistence through `ConversationManagementStore`. This is critical for maintaining sessions across network interruptions:

```swift
// From examples/MessagingUIExample/Views/ContentView.swift:49
conversationId: conversationManagementStore.conversationUUID
```

The UUID is persisted to UserDefaults, ensuring the same conversation continues when connectivity is restored, even after app restarts.

#### 2. Session Status Visibility

The UI SDK includes `MessagingSessionWidget` (v1.9.0+) that displays:
- Connection/session status
- Queue position
- Agent name with unread message counter

This provides users with visual feedback during connectivity issues, significantly improving the experience during intermittent network conditions.

**Location:** `examples/MessagingUIExample/Views/MessagingSessionWidget.swift`

#### 3. Automatic Reconnection Handling

The UI SDK (SMIClientUI) is built on top of the Core SDK and handles reconnection scenarios more gracefully out of the box. The underlying `CoreClient.start()` manages the SSE (Server-Sent Events) connection and automatic reconnection without additional implementation.

#### 4. Network State Monitoring

The example uses `GlobalCoreDelegateHandler` which implements `CoreDelegate` and receives network connectivity callbacks:

```swift
// CoreDelegate protocol method
func coreClient(_ core: CoreClient,
                didChangeNetworkState state: NetworkConnectivityState)
```

This allows you to display appropriate UI feedback during network issues.

**Location:** `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler.swift`

#### 5. Reduced Implementation Risk

Since Salesforce recommended using the sample code specifically to address connectivity issues, MessagingUIExample already demonstrates best practices for:
- Delegate registration timing (critical for proper event handling)
- Configuration management
- Session continuity
- Network state handling

### Key Implementation Points

When using MessagingUIExample as your base:

1. **Preserve conversation UUID management** - Keep the `ConversationManagementStore` pattern as-is to ensure sessions persist
2. **Enable session status widget** - Configure `MessagingSessionWidget` to show connection status to users
3. **Monitor CoreDelegate callbacks** - Customize `GlobalCoreDelegateHandler.swift` to handle network state changes appropriately for your UX
4. **Test reconnection scenarios** - Verify messages are properly queued and sent when connectivity returns

### When to Use MessagingCoreExample Instead

Only use MessagingCoreExample if you need:
- Complete custom UI that's drastically different from the provided interface
- Fine-grained control over message queuing and retry logic
- Custom network handling beyond what the SDK provides
- To build your own session persistence mechanism

However, for the specific problem of sessions being dropped during intermittent connectivity, the UI SDK was designed to handle this scenario, making **MessagingUIExample the recommended choice**.

### Additional Resources

- See `CLAUDE.md` for complete architecture documentation
- Core SDK reference: `examples/MessagingCoreExample/Models/MessagingViewModel.swift`
- UI SDK reference: `examples/MessagingUIExample/Views/ContentView.swift`
- Network state handling: `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler.swift`

---

## How do I persist conversation sessions across app restarts?

The conversation UUID must be persisted to maintain session continuity:

```swift
// BAD: New UUID each time = new conversation
let conversationID = UUID()

// GOOD: Persisted UUID = continuous conversation
let conversationID = UserDefaults.standard.getConversationUUID() ?? UUID()
UserDefaults.standard.saveConversationUUID(conversationID)
```

MessagingUIExample already implements this pattern via `ConversationManagementStore`.

**Location:** `examples/Shared/Settings/Models/ConversationManagement.swift`
