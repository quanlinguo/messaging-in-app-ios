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

Build a completely new native iOS app that includes both Experience Cloud content and native messaging.

**Good News:** The repository includes `UIKitMIAW.swift` which demonstrates **exactly this pattern** - it's a working reference implementation.

```
New Custom Native App (based on UIKitMIAW.swift)
├── WKWebView displaying Experience Cloud site
├── Native floating button overlaid on web content
└── Native Messaging (SMIClientUI SDK) launched from button
```

##### How UIKitMIAW.swift Works (Your Starting Template)

**Location:** `examples/MessagingUIExample/Views/UIKitMIAW.swift`

This file demonstrates the complete hybrid architecture:

1. **Loads Experience Cloud in WKWebView** (lines 68-79):
   ```swift
   let webView = WKWebView()
   webView.load(URLRequest(url: url))
   view = webView
   ```
   Your users see the full Experience Cloud site with all branding and navigation.

2. **Overlays Native Chat Button** (lines 147-155):
   ```swift
   self.view.addSubview(floatingButton)
   // Positioned in bottom-right corner, above web content
   ```
   A native iOS button sits on top of the web view, always accessible.

3. **Launches Native Messaging** (lines 162-196):
   ```swift
   private func openChatFeed() {
       let controller = ModalInterfaceViewController(uiConfiguration, ...)
       self.present(controller, animated: true)
   }
   ```
   Tapping the button opens the full native messaging interface.

4. **Persists Conversation UUID** (line 54):
   ```swift
   conversationId: conversationManagementStore.conversationUUID
   ```
   Sessions survive network drops and app restarts.

##### Implementation Steps

**Phase 1: Basic Adaptation (1-2 weeks)**
1. Copy `UIKitMIAW.swift` as your starting point
2. Replace `demoManagementStore.demoDomain` (line 69) with your Experience Cloud URL
3. Configure `uiConfiguration` with your Salesforce org credentials
4. Update floating button branding/position as needed
5. Test basic flow: web content loads, native chat launches

**Phase 2: Experience Cloud Integration (2-4 weeks)**
1. **Hide web-based chat button**: Update your Experience Cloud site CSS to hide the embedded messaging widget when viewed in-app
   - Use user agent detection or custom URL parameter
   - Example: `?mobile_app=true` → hide chat widget via JavaScript
2. **Handle deep links**: If users tap links in Experience Cloud, decide navigation behavior
3. **Share authentication**: Integrate Salesforce Mobile SDK for unified login (see line 62 for delegate setup)

**Phase 3: Feature Parity (4-8 weeks)**
1. Add push notifications for new messages
2. Implement mobile publisher features you rely on
3. Add native navigation chrome (if needed)
4. Testing and refinement

**Phase 4: Deployment (2-3 weeks)**
1. App Store submission
2. User migration communication
3. Gradual rollout

**Pros:**
- ✅ **Working reference implementation exists** (UIKitMIAW.swift)
- ✅ **Proven architecture** from Salesforce official samples
- ✅ **Native messaging solves connectivity issues**
- ✅ **Preserves full Experience Cloud functionality**
- ✅ **Complete control over mobile experience**
- ✅ **Conversation persistence works correctly**

**Cons:**
- ❌ **Still requires building custom native app** (can't modify mobile publisher)
- ❌ **Users must migrate** from old app to new app
- ❌ **Coordination needed**: Hide web chat button, add native button
- ❌ **Ongoing maintenance** of native iOS codebase
- ❌ **App Store submission** and approval process

**Development Effort:**
- ~3-4 months for full feature parity
- Requires iOS developers skilled in Swift/SwiftUI/UIKit
- Much faster than generic "build from scratch" because UIKitMIAW.swift provides the exact pattern

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
   - Open `examples/MessagingUIExample/Views/UIKitMIAW.swift` in Xcode
   - Replace the demo URL (line 69) with your Experience Cloud site URL
   - Configure with your Salesforce org credentials (configFile.json)
   - Run in simulator or on test device
   - Test with users experiencing connectivity issues:
     - Simulate network drops (Airplane mode on/off)
     - Force quit app and reopen - verify conversation persists
     - Measure: session persistence, reconnection behavior, user satisfaction
   - **Key validation**: Does the native SDK actually solve your connectivity problem?

2. **Calculate full replacement cost**
   - List all features in your current mobile publisher app
   - Compare with UIKitMIAW.swift capabilities
   - Identify gaps: push notifications, offline data, authentication, analytics, etc.
   - Estimate effort to achieve feature parity

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

**Starting Point:** `examples/MessagingUIExample/Views/UIKitMIAW.swift` already provides the core architecture.

**What UIKitMIAW.swift Already Includes:**
- ✅ WKWebView for web content (lines 68-79)
- ✅ Native floating button overlay (lines 147-155)
- ✅ Native messaging interface (lines 162-196)
- ✅ Conversation persistence via ConversationManagementStore (line 54)
- ✅ Configuration management (lines 36-60)
- ✅ Delegate registration (line 62)

**What You Need to Add:**

1. **Replace demo URL with your Experience Cloud URL**
   - Current: `demoManagementStore.demoDomain` (line 69)
   - Change to: Your Experience Cloud site URL

2. **Hide web chat button in Experience Cloud**
   - Add JavaScript to detect mobile app
   - Use CSS to hide embedded messaging widget
   - Example: Check user agent or custom URL parameter

3. **Authentication integration**
   - Salesforce Mobile SDK (already imported in examples)
   - See: `examples/Shared/Enums/AuthorizationMethod.swift` - Passthrough option
   - Share auth state between web view and native messaging

4. **Push notifications**
   - Register for remote notifications
   - Handle notification payloads
   - Route to messaging conversation

5. **Additional mobile publisher features** (as needed)
   - Offline support
   - Analytics tracking
   - Custom navigation
   - App-specific settings

**Development Timeline (Revised with UIKitMIAW.swift):**
- Weeks 1-2: POC validation (use UIKitMIAW.swift as-is)
- Weeks 3-4: URL replacement, basic customization
- Weeks 5-8: Hide web chat, authentication integration
- Weeks 9-12: Push notifications, feature parity
- Weeks 13-15: Testing and refinement
- Weeks 16-17: App Store submission
- **Total: ~3-4 months** (faster because UIKitMIAW.swift exists)

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

### Key Insight: UIKitMIAW.swift is Your Blueprint

The most important discovery is that **Salesforce already provides a working reference implementation** for the exact hybrid architecture you need:

**File:** `examples/MessagingUIExample/Views/UIKitMIAW.swift`

This file demonstrates:
- Loading web content (Experience Cloud) in WKWebView
- Overlaying a native chat button on top of web content
- Launching native messaging that persists across connectivity issues
- Managing conversation UUID for session continuity

**This is not a theoretical approach - it's working sample code you can adapt.**

Instead of building from scratch, you're adapting proven code that demonstrates the recommended pattern for combining web content with native messaging.

### Summary Decision Matrix

| Scenario | Recommendation |
|----------|---------------|
| **POC phase** | Run UIKitMIAW.swift with your Experience Cloud URL - validate it solves connectivity issues |
| **Connectivity issues are severe/widespread** | Option 1 - Build custom app starting from UIKitMIAW.swift |
| **Connectivity issues affect small subset** | Option 2 - Separate messaging app, or work with Salesforce on mobile publisher improvements |
| **Unclear if native SDK helps** | Start with POC before committing resources |
| **No iOS development resources** | Hire iOS developers or contract firm, starting point already exists (UIKitMIAW.swift) |

### Additional Resources

- **Primary reference**: `examples/MessagingUIExample/Views/UIKitMIAW.swift` (the hybrid architecture blueprint)
- [Salesforce Mobile SDK Integration](https://developer.salesforce.com/docs/atlas.en-us.mobile_sdk.meta/mobile_sdk/intro.htm)
- Native SDK Authentication: `CLAUDE.md` - "Authentication Methods" section
- Example passthrough auth: `examples/Shared/Delegates/Providers/PassthroughVerificationStore.swift`
- WKWebView integration: Apple's [WKWebView documentation](https://developer.apple.com/documentation/webkit/wkwebview)
- Conversation persistence: `examples/Shared/Settings/Models/ConversationManagement.swift`
