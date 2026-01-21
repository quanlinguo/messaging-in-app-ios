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

---

## Is the native SDK a standalone app? How does it work with our existing Experience Cloud mobile publisher app?

**Question:** Our existing mobile publisher app is for an Experience Cloud site (we have no source code for it). When I look at the recommended MessagingUIExample, is this intended to be a standalone native mobile app? If so, how would I replace our existing mobile publisher app with this approach while still supporting a full Experience Cloud site with an embedded chat button?

**Answer:** This is a critical architectural decision. Since your mobile publisher app is Salesforce-generated (no source code access), you **cannot integrate the native SDK into your existing app**. You have two distinct paths forward:

### Understanding Your Current Situation

#### Salesforce Mobile Publisher App (Current)
- **No source code access**: Generated by Salesforce Mobile Publisher
- **Web view wrapper**: Displays your Experience Cloud site in a mobile app shell
- **Embedded chat**: JavaScript-based chat widget within the web view
- **Session management**: Browser-based (cookies/localStorage)
- **Network handling**: Standard web browser networking
- **Cannot be modified**: No ability to add native SDK integration

#### Native SDK Approach (MessagingUIExample)
- **Custom native app**: Requires building from scratch
- **Full source code control**: You own and maintain the codebase
- **Native Swift/SwiftUI**: Direct SDK integration at native layer
- **Persistent sessions**: Conversation UUID in UserDefaults
- **Native networking**: SSE with built-in reconnection
- **Full customization**: Complete control over UX

### Your Two Options

#### Option 1: Build Custom Native App (Replace Mobile Publisher)

Build a completely new native iOS app that includes both Experience Cloud content and native messaging:

```
New Custom Native App (built from MessagingUIExample)
├── Native Navigation/UI Chrome
├── Native Messaging (SMIClientUI SDK)
└── WKWebView for Experience Cloud content
    └── Hide web-based chat button (users use native messaging instead)
```

**This means:**
- Starting from MessagingUIExample as your foundation
- Adding WKWebView to display your Experience Cloud site
- Implementing navigation between native messaging and web content
- Users download your NEW custom app (not the mobile publisher app)
- Distributing through App Store as your own branded app

**Pros:**
- Complete control over mobile experience
- Native messaging solves connectivity issues
- Can optimize UX for mobile specifically
- Full access to iOS native features
- Conversation persistence works correctly

**Cons:**
- **Major undertaking**: Building entire app from scratch
- Must replicate mobile publisher functionality (push notifications, offline support, etc.)
- Ongoing native iOS development and maintenance required
- Users must migrate from old app to new app
- App Store submission and approval process
- Need native iOS development expertise

**Development Effort:**
- ~2-6 months depending on feature parity requirements
- Requires iOS developers skilled in Swift/SwiftUI
- Ongoing maintenance and updates

#### Option 2: Keep Mobile Publisher, Build Separate Messaging App

Deploy native messaging as a separate, lightweight app while keeping the mobile publisher app:

```
User's Device
├── Experience Cloud Mobile Publisher App (existing - unchanged)
│   └── Has embedded web chat
└── [YourCompany] Messaging App (new - standalone native messaging)
    └── For users with connectivity issues
```

**This means:**
- MessagingUIExample becomes a standalone "messaging-only" app
- Users experiencing connectivity issues can download this separate app
- Two apps on user's device (not ideal UX)
- Gradually migrate users from web chat to native messaging app

**Pros:**
- Minimal development effort (just MessagingUIExample + branding)
- Mobile publisher app unchanged (lower risk)
- Can pilot with subset of users
- Quick time to market (weeks instead of months)

**Cons:**
- **Poor user experience**: Two separate apps
- Confusing for users (which app to use?)
- Cannot access Experience Cloud content from messaging app
- Session continuity issues if users switch between apps
- Not a long-term solution

### Critical Limitations You Cannot Avoid

Since you don't have source code access to the mobile publisher app:

❌ **Cannot** add native SDK to existing mobile publisher app
❌ **Cannot** modify how the mobile publisher app handles networking
❌ **Cannot** integrate native session persistence into mobile publisher
❌ **Cannot** replace the embedded chat widget with native messaging in mobile publisher
❌ **Cannot** add custom native features to mobile publisher

✅ **Can only** build a completely new custom native app (Option 1)
✅ **Or** build a separate standalone messaging app (Option 2)

### Recommended Path Forward

Given your constraints (no source code, connectivity issues), here's the pragmatic recommendation:

#### Immediate Term: Proof of Concept (2-4 weeks)

1. **Evaluate the native SDK's effectiveness**
   - Build MessagingUIExample with your Salesforce org configuration
   - Test with users experiencing connectivity issues
   - Validate that native SDK actually solves the problem
   - Measure: session persistence, reconnection behavior, user satisfaction

2. **Calculate full replacement cost**
   - List all features in your current mobile publisher app
   - Estimate effort to build custom native app with those features
   - Consider: push notifications, offline data, authentication, analytics, etc.

#### Decision Point

After POC, you must decide:

**A. Full Replacement Justified?**
- If connectivity issues are severe and widespread
- If you have budget/team for custom native app development
- If you want long-term control over mobile experience
→ **Proceed with Option 1** (build custom native app)

**B. Not Worth Full Replacement?**
- If connectivity issues affect only small user segment
- If budget/resources are limited
- If mobile publisher app otherwise meets needs
→ **Consider alternatives**:
  - Deploy Option 2 (separate messaging app) as stopgap
  - Work with Salesforce to improve mobile publisher networking
  - Accept current limitations
  - Direct high-value users to native messaging app only

### Critical Questions for Salesforce

Before committing to either option, ask Salesforce:

1. **Is there a roadmap to improve mobile publisher networking/session handling?**
   - They may be planning fixes that solve your issue
   - Timeline for improvements?

2. **Can the mobile publisher app be customized with native plugins/extensions?**
   - Some mobile publisher frameworks allow native modules
   - Even if not currently supported, worth asking

3. **What's Salesforce's official recommendation for this scenario?**
   - They suggested native SDK - but did they understand you have no source code?
   - Do they have reference architectures for Experience Cloud + Native SDK?

4. **Can web chat and native SDK share conversation history?**
   - Critical for user experience if you run both
   - API for migrating conversations?

5. **What authentication method should we use?**
   - If building custom app: Passthrough auth with Salesforce Mobile SDK?
   - JWT-based user verification?
   - How to maintain user identity across web and native?

### If You Proceed with Option 1 (Custom Native App)

**Minimum Requirements to Replace Mobile Publisher:**

Starting from MessagingUIExample, you'll need to add:

1. **WKWebView integration** for Experience Cloud content
   ```swift
   import WebKit

   struct ExperienceCloudView: UIViewRepresentable {
       let url: URL
       func makeUIView(context: Context) -> WKWebView { ... }
   }
   ```

2. **Navigation structure** between native and web content
   - Tab bar or navigation controller
   - Deep linking handling

3. **Authentication**
   - Salesforce Mobile SDK integration (already in examples)
   - See: `examples/Shared/Enums/AuthorizationMethod.swift` - Passthrough option
   - OAuth flow for Salesforce login

4. **Push notifications**
   - Register for remote notifications
   - Handle notification payloads
   - Route to messaging or web content

5. **Feature parity with mobile publisher**
   - Offline support (if needed)
   - Analytics tracking
   - App configuration/settings
   - Whatever else your users rely on

**Development Timeline (Estimated):**
- Weeks 1-2: POC validation
- Weeks 3-6: Core app structure + Experience Cloud integration
- Weeks 7-10: Authentication + push notifications
- Weeks 11-14: Feature parity + testing
- Weeks 15-18: Beta testing + refinement
- Weeks 19-20: App Store submission
- **Total: ~4-5 months minimum**

### If You Proceed with Option 2 (Separate Messaging App)

**Deployment Strategy:**

1. Brand MessagingUIExample as "[YourCompany] Support Chat"
2. Deploy to App Store as standalone app
3. Notify users with connectivity issues
4. Provide clear instructions: "For chat support, download [YourCompany] Support Chat app"
5. Consider deprecating web chat for mobile users over time

**User Experience:**
- Two apps is awkward but may be acceptable as interim solution
- Clear communication about which app to use for what
- Eventually migrate all mobile users to separate messaging app
- Deprecate mobile publisher app when feasible

### Additional Resources

- [Salesforce Mobile SDK Integration](https://developer.salesforce.com/docs/atlas.en-us.mobile_sdk.meta/mobile_sdk/intro.htm)
- Native SDK Authentication: `CLAUDE.md` - "Authentication Methods" section
- Example passthrough auth: `examples/Shared/Delegates/Providers/PassthroughVerificationStore.swift`
- WKWebView integration: Apple's [WKWebView documentation](https://developer.apple.com/documentation/webkit/wkwebview)
