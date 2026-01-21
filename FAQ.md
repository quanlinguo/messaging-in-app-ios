# Frequently Asked Questions

## Should I build a POC first to validate the native SDK solves my connectivity issues?

**Question:** Before committing to building a custom native app and migrating all users, should I first validate that the native SDK actually solves our intermittent connectivity issues?

**Answer:** **YES, absolutely!** This is the correct risk management approach.

### Why POC First?

Before committing to a 3-4 month development effort to build a custom native app (based on UIKitMIAW.swift), you should validate your hypothesis:

**Hypothesis to Test:**
> "The native Messaging SDK solves our intermittent connectivity and session loss issues better than the web-based approach."

**If native SDK DOESN'T solve the problem** → Don't build the custom app
**If native SDK DOES solve the problem** → You have a clear business case to proceed

### Quick POC Validation (2-4 Weeks)

#### Week 1: Setup & Initial Testing

**Minimal Configuration Steps:**

1. **Open the sample project**
   ```bash
   cd messaging-in-app-ios
   open MessagingExample.xcworkspace
   ```

2. **Configure UIKitMIAW.swift**
   - File: `examples/MessagingUIExample/Views/UIKitMIAW.swift`
   - Line 69: Replace demo URL with your Experience Cloud URL
   - Example: `guard let url = URL(string: "https://your-experience-cloud.force.com") else { ... }`

3. **Configure Salesforce credentials**
   - File: `examples/MessagingUIExample/configFile.json`
   ```json
   {
       "OrganizationId": "YOUR_ORG_ID",
       "DeveloperName": "YOUR_DEPLOYMENT_NAME",
       "Url": "https://YOUR_ORG.salesforce-scrt.com"
   }
   ```

4. **Set Passthrough authentication**
   - Run app in simulator
   - Open Settings menu
   - Set "Authorization Method" to "Passthrough"
   - Login with Salesforce credentials

5. **Document current web behavior**
   - Test same scenarios in your mobile publisher app
   - Document exactly what happens during network issues
   - Establish baseline for comparison

#### Week 2: Network Resilience Testing

**Critical Test Scenarios:**

| Scenario | How to Test | Expected (Native) | Current (Web) |
|----------|-------------|-------------------|---------------|
| **Network drop during chat** | Enable Airplane mode mid-conversation | Session persists, messages queue | Session lost? |
| **Network reconnection** | Disable Airplane mode | Queued messages send, conversation continues | New session? |
| **App force quit** | Force quit app, reopen | Same conversation loads | Session lost? |
| **App background** | Background 5+ minutes, then foreground | Conversation intact | Timeout? |
| **Weak connectivity** | Network Link Conditioner: 3G/Edge | Handles gracefully | Errors/timeouts? |
| **Complete offline** | Turn off WiFi and cellular | Clear offline state, auto-retry | Unclear state? |

**Testing Tools:**
- **Airplane mode** - Quick on/off network testing
- **Network Link Conditioner** - Xcode → Settings → Network Link Conditioner
  - Simulates: 3G, Edge, Very Bad Network, 100% Loss
- **Charles Proxy** - Monitor network traffic and latency
- **Xcode Console** - Watch for reconnection logs in SMIClientCore

**What to Monitor:**
```
Settings → Logging → Enable Core Logging
Settings → Logging → Enable UI Logging

// Watch for in console:
- "SSE connection established"
- "SSE connection lost"
- "Reconnecting..."
- ConversationUUID persistence
```

#### Week 3: Measure Success Criteria

**Critical Metrics:**

✅ **Session Persistence**
- [ ] Conversation UUID persists across network drops
- [ ] Messages stay in history after reconnection
- [ ] Users can resume conversations after app restart
- [ ] Same conversation continues after force quit

✅ **Message Delivery**
- [ ] Messages sent during connectivity loss deliver when network returns
- [ ] No duplicate messages
- [ ] Message order preserved
- [ ] Typing indicators work correctly

✅ **User Experience**
- [ ] Connection status clearly visible (MessagingSessionWidget)
- [ ] Error messages helpful and actionable
- [ ] Reconnection happens automatically
- [ ] No crashes or freezes during network transitions

✅ **Performance**
- [ ] Reconnection time < 5 seconds
- [ ] App handles poor network gracefully
- [ ] UI remains responsive during reconnection
- [ ] Battery impact acceptable

**Quantify the Improvement:**
- Current web app: X% of sessions lost during network issues
- Native SDK: Y% of sessions persisted
- **Is the improvement significant enough to justify the investment?**

#### Week 4: User Validation (Highly Recommended)

**Real-World Testing:**

1. **Identify test users**
   - 5-10 users who currently experience connectivity issues
   - Mix of network conditions (WiFi, cellular, poor coverage areas)

2. **Deploy TestFlight build**
   - Build UIKitMIAW.swift POC
   - Distribute via TestFlight
   - Provide clear testing instructions

3. **Gather feedback**
   - Survey questions:
     - "Did you experience session loss?"
     - "Did messages deliver reliably?"
     - "How was the reconnection experience?"
     - "Is this better than the current app?"

4. **Measure actual improvement**
   - User satisfaction scores
   - Reported session loss incidents
   - Message delivery failures
   - Support ticket reduction

### Decision Gates After POC

#### ✅ If Native SDK Solves the Problem

**You Have Proof:**
- Native SDK handles connectivity measurably better
- Users confirm improved experience
- Session persistence works as expected
- Clear business case for custom app development

**Next Steps:**
1. Present POC results to stakeholders
2. Get approval for 3-4 month custom app development
3. Plan user migration strategy
4. Estimate full feature parity requirements (push notifications, etc.)
5. Begin full development based on UIKitMIAW.swift template

**Expected Timeline:**
- POC: 2-4 weeks (completed)
- Full Development: 3-4 months (from FAQ Option 1)
- **Total: ~4-5 months to production**

**Cost-Benefit Analysis:**
- Development cost: [X months of iOS developer time]
- User migration effort: [Communication, support, training]
- Ongoing maintenance: [Native app updates, App Store management]
- **vs.**
- Cost of current connectivity issues: [Support tickets, user churn, poor experience]

#### ❌ If Native SDK DOESN'T Solve the Problem

**Don't Proceed with Custom App** - building it won't solve your problem!

**Alternative Options:**

1. **Work with Salesforce**
   - Ask about mobile publisher improvements roadmap
   - Request enterprise support for connectivity issues
   - Explore other Salesforce solutions

2. **Improve web-based approach**
   - Implement service workers for offline support
   - Better session management in web chat
   - Progressive Web App (PWA) enhancements

3. **Hybrid partial solution**
   - Deploy separate messaging app for users with issues (FAQ Option 2)
   - Keep mobile publisher for majority of users
   - Gradually migrate only problem users

4. **Accept limitations**
   - If issue affects only small subset of users
   - If cost of solution exceeds cost of problem
   - Communicate limitations clearly to users

### POC Success Checklist

Before proceeding to full development, ensure:

- [ ] Native SDK demonstrably solves connectivity issues
- [ ] Improvement is quantified (X% to Y% session retention)
- [ ] Real users validate the improvement
- [ ] Business case is clear (ROI positive)
- [ ] Stakeholders approve full development
- [ ] Development team is committed (3-4 months)
- [ ] User migration plan is defined
- [ ] Feature parity requirements are documented
- [ ] Ongoing maintenance plan is in place

### Critical Questions to Answer

1. **Does native SDK solve the connectivity problem?** (Primary validation)
2. **By how much?** (Quantify: 10% improvement? 90% improvement?)
3. **What's the user impact?** (Real users confirm improvement)
4. **What's the development cost?** (Time, resources, opportunity cost)
5. **What's the migration impact?** (How many users, communication strategy)
6. **What about feature parity?** (Push notifications, offline data, analytics, etc.)
7. **What's the ROI?** (Development cost vs cost of connectivity issues)
8. **What are the risks?** (App Store approval, user adoption, ongoing maintenance)

### Why This Approach is Sound Risk Management

✅ **Minimizes wasted effort** - 2-4 weeks vs 3-4 months
✅ **Provides data for decisions** - Not guessing, validating
✅ **Validates assumptions** - Does native really solve the problem?
✅ **Creates working demo** - Show stakeholders actual solution
✅ **Identifies unforeseen issues** - Discover problems early
✅ **Builds confidence** - Team and stakeholders see proof
✅ **Enables informed go/no-go** - Clear decision criteria

### POC Deliverables

**At end of POC, you should have:**

1. **Working demo** - UIKitMIAW.swift configured for your org
2. **Test results** - Data from all network scenarios
3. **Comparison metrics** - Native vs web performance
4. **User feedback** - Real users validate improvement
5. **Decision document** - Go/no-go recommendation with justification
6. **Cost estimate** - Full development timeline and resources
7. **Migration plan** - How to transition users from mobile publisher

**This 2-4 week investment protects you from 3-4 months of potentially unnecessary development.**

---

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

## What is the relationship between the Messaging SDK and Salesforce Mobile SDK?

**Question:** When building Salesforce mobile apps, I've always used the Salesforce Mobile SDK. When I look at this sample code, I'm unclear whether it uses the Salesforce Mobile SDK, is built on top of it, or represents a different approach altogether.

**Answer:** The **Messaging for In-App SDK is a completely separate, standalone SDK** that does NOT require the Salesforce Mobile SDK to function. However, the Salesforce Mobile SDK can be used OPTIONALLY for one specific authentication method.

### The Two SDKs Are Independent

#### Messaging for In-App SDK (This Repository)
- **Package**: `Swift-Package-InAppMessaging` from `https://github.com/Salesforce-Async-Messaging/Swift-Package-InAppMessaging.git`
- **Frameworks**: `SMIClientCore` and `SMIClientUI`
- **Purpose**: Messaging/chat functionality for customer support
- **Operates independently**: Does NOT require Salesforce Mobile SDK
- **Can be used in**: Any iOS app, not just Salesforce-integrated apps

#### Salesforce Mobile SDK (Optional Dependency)
- **Package**: `SalesforceMobileSDK-iOS-SPM` from `https://github.com/forcedotcom/SalesforceMobileSDK-iOS-SPM`
- **Frameworks**: `SalesforceSDKCore`, `MobileSync`, `SalesforceAnalytics`
- **Purpose**: Salesforce platform integration, OAuth authentication, data sync
- **Used for**: Passthrough authentication only (in these examples)

### Three Authentication Methods

The Messaging SDK supports three authentication approaches:

**Location:** `examples/Shared/Enums/AuthorizationMethod.swift`

1. **Unverified** - No authentication
   - Simplest approach
   - No user identity verification
   - **Use case:** Public messaging, anonymous support
   - ✅ **No Salesforce Mobile SDK required**

2. **User Verified (JWT)** - Your app provides a JWT token
   - You implement JWT generation in your backend
   - Pass token via `UserVerificationDelegate`
   - **Use case:** Users with non-Salesforce accounts (your own auth system)
   - **Example:** Retail app where customers have company accounts, not Salesforce credentials
   - ✅ **No Salesforce Mobile SDK required**
   - **Location:** `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler+UserVerification.swift:18-19`

3. **Passthrough** - Uses Salesforce Mobile SDK for authentication
   - Leverages Salesforce Mobile SDK OAuth login
   - Automatically retrieves JWT token from Salesforce
   - **Use case:** Users with Salesforce credentials (including Experience Cloud users)
   - **Example:** Experience Cloud site users who log in with Salesforce credentials
   - ❌ **Requires Salesforce Mobile SDK**
   - **Location:** `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler+UserVerification.swift:21-23`
   - **Implementation:** Lines 32-45 use `AuthHelper.loginIfRequired` from SalesforceSDKCore

### IMPORTANT: For Experience Cloud Users

**If your users are Experience Cloud users who authenticate with Salesforce credentials, you MUST use Passthrough authentication, which means you NEED both SDKs:**

- ✅ **Messaging SDK** (SMIClientCore/UI) - For messaging functionality
- ✅ **Salesforce Mobile SDK** (SalesforceSDKCore) - For authentication

**Why:**
- Your users have **Salesforce Experience Cloud accounts**
- They authenticate using **Salesforce OAuth** (same credentials as your Experience Cloud site)
- Passthrough authentication provides single sign-on experience
- Mobile SDK handles Salesforce OAuth properly

**Configuration for Experience Cloud:**
```swift
import SMIClientCore
import SMIClientUI
import SalesforceSDKCore  // Required for Experience Cloud users

let config = Configuration(...)
config.userVerificationRequired = true  // Enable authentication

// Set authorization method to Passthrough
authorizationMethod = .passthrough

// UserVerificationDelegate will use Mobile SDK's AuthHelper
// See: GlobalCoreDelegateHandler+UserVerification.swift:21-23
```

### How Passthrough Authentication Works (When Mobile SDK IS Used)

**File:** `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler+UserVerification.swift`

```swift
// Line 22-23: Passthrough method
case .passthrough:
    await Self.salesforceLogin()  // Uses Salesforce Mobile SDK
    return await Self.fetchMIAWJWT(core)

// Lines 38-44: Calls Salesforce Mobile SDK login
AuthHelper.loginIfRequired {
    // User authenticated via Salesforce OAuth
}

// Lines 47-70: Fetches messaging JWT using Salesforce credentials
let request = RestRequest(method: .GET,
                         path: core.salesforceAuthenticationRequestPath)
```

**Passthrough flow:**
1. User logs into Salesforce using Mobile SDK OAuth (standard Salesforce login screen)
2. App uses Salesforce session to call messaging authentication endpoint
3. Salesforce returns messaging-specific JWT token
4. Messaging SDK uses that JWT for conversations

### Package Dependencies in the Examples

**MessagingCoreExample** (Minimal):
```
Dependencies:
├── SMIClientCore (required)
└── [No Salesforce Mobile SDK]
```

**MessagingUIExample** (Includes Optional Mobile SDK):
```
Dependencies:
├── SMIClientCore (required)
├── SMIClientUI (required)
└── SalesforceMobileSDK-iOS-SPM (optional - only for Passthrough auth)
    ├── SalesforceSDKCore
    ├── MobileSync
    └── SalesforceAnalytics
```

### Key Architectural Insight

**The Messaging SDK is NOT built on top of Salesforce Mobile SDK.**

They are separate SDKs that can optionally work together:

```
Your App
├── Messaging SDK (SMIClientCore/UI) - STANDALONE
│   └── Works independently for messaging
└── Salesforce Mobile SDK (optional)
    └── Only used if you choose Passthrough authentication
```

### When Should You Use Each SDK?

| Scenario | Use Messaging SDK | Use Mobile SDK | Auth Method |
|----------|-------------------|----------------|-------------|
| **Experience Cloud users (Salesforce credentials)** | ✅ Yes | ✅ Yes (REQUIRED) | Passthrough |
| **App already uses Salesforce Mobile SDK** | ✅ Yes | ✅ Yes | Passthrough |
| **Users have Salesforce accounts** | ✅ Yes | ✅ Yes (REQUIRED) | Passthrough |
| **Users have non-Salesforce accounts (your own auth)** | ✅ Yes | ❌ Not needed | JWT |
| **No user authentication needed** | ✅ Yes | ❌ Not needed | Unverified |
| **Need Salesforce data sync, offline, platform features** | Optional | ✅ Yes | Passthrough |

### Practical Examples

**Scenario 1: Experience Cloud Native App (REQUIRES BOTH SDKs)**
```swift
// Your users are Experience Cloud users with Salesforce credentials
import SMIClientUI
import SMIClientCore
import SalesforceSDKCore  // REQUIRED for Salesforce authentication

let config = Configuration(...)
config.userVerificationRequired = true  // Authentication required

// Use Passthrough authentication
authorizationMethod = .passthrough

// UserVerificationDelegate uses Mobile SDK's AuthHelper
// When user taps chat, they'll use Salesforce OAuth login
// Same credentials as they use for Experience Cloud site
// See: GlobalCoreDelegateHandler+UserVerification.swift:21-23
```

**Scenario 2: Customer Service App with Non-Salesforce Users (NO Mobile SDK)**
```swift
// Your users have accounts in YOUR system, not Salesforce
// Example: Retail customers with company accounts
import SMIClientUI
import SMIClientCore
// NO SalesforceSDKCore import

let config = Configuration(...)
config.userVerificationRequired = true

// Implement UserVerificationDelegate to provide JWT
func core(_ core: CoreClient,
          userVerificationChallengeWith reason: ChallengeReason) async -> UserVerification? {

    // Call YOUR backend to generate JWT for this user
    let jwt = await YourBackend.generateMessagingJWT(customerId: currentUser.id)

    return UserVerification(customerIdentityToken: jwt, type: .JWT)
}
// See: GlobalCoreDelegateHandler+UserVerification.swift:18-19
```

**Scenario 3: Anonymous Messaging (NO Mobile SDK)**
```swift
// Public messaging, no authentication
import SMIClientUI
import SMIClientCore

let config = Configuration(...)
config.userVerificationRequired = false  // Unverified mode

let uiConfig = UIConfiguration(configuration: config, ...)
Interface(uiConfig, ...)  // Works without Mobile SDK
```

### Bottom Line

- **Messaging SDK = Standalone messaging/chat SDK**
- **Salesforce Mobile SDK = Authentication SDK for Salesforce users**
- They are **separate products** that can optionally integrate
- **If your users have Salesforce credentials (including Experience Cloud):** You NEED both SDKs
- **If your users have non-Salesforce accounts (your own auth):** You only need Messaging SDK
- **If no authentication needed:** You only need Messaging SDK

### Decision Tree: Do I Need Mobile SDK?

```
Do your users log in with Salesforce credentials?
├─ YES (Experience Cloud, Salesforce users)
│  └─ ✅ Need BOTH: Messaging SDK + Mobile SDK (Passthrough auth)
│
└─ NO (Your own auth system, or no auth)
   └─ ✅ Need ONLY: Messaging SDK (JWT or Unverified)
```

This is different from Salesforce's traditional approach where mobile features required the Mobile SDK. The Messaging SDK is purpose-built for messaging and can work independently OR with Mobile SDK depending on your authentication requirements.

### For Your Experience Cloud + UIKitMIAW.swift App

Based on your scenario (Experience Cloud users with Salesforce credentials):

**Required Dependencies:**
```
Your Custom Native App (built from UIKitMIAW.swift)
├── SMIClientCore ✅ REQUIRED (messaging core)
├── SMIClientUI ✅ REQUIRED (messaging UI)
└── SalesforceMobileSDK-iOS-SPM ✅ REQUIRED (authentication)
    ├── SalesforceSDKCore (OAuth login)
    ├── MobileSync (optional - can exclude if not needed)
    └── SalesforceAnalytics (optional - can exclude if not needed)
```

**User Flow:**
1. User launches your custom app
2. **Salesforce Mobile SDK OAuth login** (SalesforceSDKCore)
   - User enters their Experience Cloud credentials
   - Same Salesforce login as web
3. App loads Experience Cloud site in WKWebView (UIKitMIAW.swift pattern)
4. Native chat button overlaid on web content
5. User taps chat → Messaging SDK launches
   - **Uses Passthrough authentication**
   - Messaging SDK leverages the Mobile SDK session
   - No second login required

**Implementation Reference:**
- Auth configuration: `examples/Shared/Enums/AuthorizationMethod.swift` → `.passthrough`
- Auth implementation: `examples/Shared/Delegates/CoreDelegate/GlobalCoreDelegateHandler+UserVerification.swift:21-45`
- UIKitMIAW pattern: `examples/MessagingUIExample/Views/UIKitMIAW.swift`

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

Q: When it comes to building a mobile app for Salesforce, I’m aware of the Salesforce Mobile SDK and have always understood that mobile apps are built using that SDK. However, when I look at the sample code provided here, I’m unclear whether it uses the Salesforce Mobile SDK, is built on top of the Mobile SDK, or represents a different or newer approach altogether.

Based on the source code provided, the `MessagingUIExample` app **does use the Salesforce Mobile SDK**, but it treats it as a complementary library rather than the sole foundation.

Here is the breakdown of how the sample code utilizes these SDKs:

### 1. It Uses Two Distinct SDKs
The sample application integrates two separate SDK packages that work side-by-side:

*   **Messaging for In-App SDK (`SMIClientUI`, `SMIClientCore`):** This is the newer, specialized SDK responsible for the "Advanced Messaging" features, chat feed UI, and the session persistence you are interested in.
*   **Salesforce Mobile SDK (`SalesforceSDKCore`, `MobileSync`, etc.):** This is the traditional SDK you are familiar with. The sample app includes it as a dependency to handle authentication and identity,.

### 2. Evidence of Salesforce Mobile SDK Usage
The `MessagingUIExample` is explicitly built with the Salesforce Mobile SDK included in its project dependencies.

*   **Project Dependencies:** The project file (`project.pbxproj`) lists `SalesforceMobileSDK-iOS-SPM` as a remote package dependency. It specifically links frameworks such as `SalesforceSDKCore`, `SalesforceSDKCommon`, and `SalesforceAnalytics`.
*   **Code Imports:** Several critical files in the sample code import `SalesforceSDKCore` alongside the messaging libraries. For example, `GlobalCoreDelegateHandler.swift` imports both `SMIClientCore` and `SalesforceSDKCore`.

### 3. How the Mobile SDK is Used in the Sample
In this specific sample app, the Salesforce Mobile SDK is used primarily for **User Verification (Authentication)**.

*   **Login Logic:** The app uses the Mobile SDK's `AuthHelper` to handle logging into Salesforce.
    *   *Source Evidence:* In `GlobalCoreDelegateHandler+UserVerification.swift`, the code calls `AuthHelper.loginIfRequired` to authenticate the user against your Salesforce Org.
*   **Token Retrieval:** It uses the Mobile SDK's `RestClient` to make authenticated API calls to fetch the JSON Web Tokens (JWT) required to verify the user's identity in the messaging session.
    *   *Source Evidence:* The function `fetchMIAWJWT` uses `RestClient.shared.send(request: request)` to retrieve the access token.
*   **Session Management:** The settings menu includes a button to logout using `UserAccountManager.shared.logout()`, which is a standard Salesforce Mobile SDK method.

### Summary for Your Implementation
You are not abandoning the Salesforce Mobile SDK approach. Instead, this sample demonstrates a **hybrid approach**:

1.  **Salesforce Mobile SDK:** Handles the "plumbing" of logging the user in and managing their Salesforce credentials.
2.  **Messaging for In-App SDK:** Handles the actual chat interface, message history, and the network resilience features you need to fix your connectivity issues.

