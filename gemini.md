https://notebooklm.google.com/notebook/5b50a865-5b8a-4110-9bbc-4c0977a5b7eb

Q: I have a mobile publisher app for advanced messaging. The issue is that users experience intermittent network connectivity. When connectivity is lost, active sessions are dropped, which leads to a poor user experience. Even when connectivity is restored, user sessions are lost, which is frustrating. Salesforce has suggested building a native app using the provided sample code to address the connectivity issues. Given this, I would like to know which sample app would be most appropriate to use.

A: Based on the Salesforce Messaging for In-App iOS sources provided, the **MessagingUIExample (Simple UI SDK Example)** is the most appropriate sample app for you to use as a foundation to address your connectivity and dropped session issues.

Here is a detailed explanation of why this is the correct choice based on your specific requirements:

### 1. Built-in Session Persistence (Solving the "Lost Session" Issue)
The primary cause of lost sessions when connectivity drops or the app restarts is often the failure to retain the `conversationId`. If an app generates a new ID every time it reconnects, the user loses their history.

*   **MessagingUIExample** contains a robust implementation of session management. It demonstrates how to persist the conversation UUID using `UserDefaults` so that the same session is resumed even after the app closes or loses connectivity.
*   **Source Evidence:** In `ConversationManagementSettings.swift`, the code explicitly checks for an existing conversation ID before generating a new one:
    > "We have a chicken & egg situation here where we need to determine when to automatically generate a new conversationId if the old one is reset. If result.isEmpty { ... }".
*   **Contrast with Core Example:** The **MessagingCoreExample** uses a hardcoded random UUID generator by default and only warns you via comments to handle persistence yourself:
    > "// This code uses a random UUID for the conversation ID, but // be sure to use the same ID if you want to continue the // same conversation after a restart".

### 2. Robust UI and State Handling
Your query mentions that the current user experience is "poor" due to connectivity drops. The **MessagingUIExample** leverages the Salesforce **UI SDK**, which is designed to handle complex state changes (like message history retrieval) automatically, whereas the Core SDK requires you to build the user interface and logic from scratch.

*   **MessagingUIExample** uses the `SMIClientUI` framework, providing an out-of-the-box UI that handles message rendering, typing indicators, and attachments,.
*   The UI SDK automatically manages the `ChatFeed` view.
*   **MessagingCoreExample**, by comparison, requires you to manually implement the logic for `didChangeNetworkState` and manually append entries to the view when they arrive. Using the Core SDK would increase the risk of creating a poor UX if edge cases regarding network recovery aren't perfectly implemented in your custom code.

### 3. Connection and Configuration Management
The **MessagingUIExample** provides a more advanced setup for managing connection environments and configurations, which is essential for testing network resilience.

*   It includes a `ConnectionEnvironment` enum that allows you to toggle between different configurations easily,.
*   It includes a dedicated `ConversationManagementStore` to manage the lifecycle of the conversation UUID.

### Recommendation
You should download the **MessagingUIExample** found in the `examples/MessagingUIExample/` folder.

**To implement the fix for your connectivity issue:**
1.  Focus on the `ConversationManagementStore` logic found in `ConversationManagementSettings.swift`.
2.  Ensure your native app generates a UUID **once**, stores it locally (e.g., in `UserDefaults` or Keychain), and reuses that specific UUID when initializing the `UIConfiguration` or `Interface`.
3.  This ensures that even if the network drops and the client reconnects, Salesforce recognizes the user as the same participant and restores their session history.

---

Q: Our existing mobile publisher app is for an Experience Cloud site. When I look at the app you are recommending, is this intended to be a standalone native mobile app? If so, how would I replace our existing mobile publisher app with this approach while still supporting a full Experience Cloud site with an embedded chat button? 

Yes, the `MessagingUIExample` is a standalone native iOS application. However, **you do not need to abandon your Experience Cloud site**.

To replace your existing Mobile Publisher app while retaining your Experience Cloud site, you should adopt a **Hybrid Architecture** demonstrated specifically in the source file `UIKitMIAW.swift` within the `MessagingUIExample`.

This approach allows you to build a native iOS "wrapper" that displays your Experience Cloud site while running the Messaging for In-App (MIAW) feature natively to solve your connectivity and session loss issues.

Here is how you would implement this replacement using the provided sources:

### 1. The Container: Load Experience Cloud in a WebView
Instead of using Mobile Publisher, you will build a simple native app that serves as a browser for your site. The `MessagingUIExample` demonstrates this in the `UIKitMIAW` struct.

*   The code initializes a `WKWebView`, which is the standard iOS component for displaying web content.
*   It loads your specific URL (in this case, your Experience Cloud site URL) into that view: `webView.load(URLRequest(url: url))`,.
*   This ensures your users still see your full Experience Cloud site, branding, and navigation, exactly as they do now.

### 2. The Chat Button: Use a Native Overlay
The critical change to solve your connectivity issue is that the **chat button and chat window must be native**, not embedded in the HTML of your site.

*   **Hide the Web Button:** You should update your Experience Cloud site configuration to hide the standard embedded messaging button when viewed inside this mobile app (e.g., via CSS or audience targeting).
*   **Add the Native Button:** The sample code demonstrates how to place a native iOS button on top of the web content.
    *   The code creates a `floatingButton` (a native `UIButton`).
    *   It adds this button to the view hierarchy *above* the web view: `self.view.addSubview(floatingButton)`.
    *   This button remains visible and interactive regardless of the web page's loading state or network hiccups.

### 3. The Chat Session: Native Execution
When the user taps the native floating button, it launches the Salesforce UI SDK, which is far more resilient to network changes than the web-based client.

*   **Launching the Chat:** The button triggers the `openChatFeed` function,.
*   **Native Interface:** This function initializes `InterfaceViewController`, which is the native messaging window provided by the SDK, and pushes it onto the screen or presents it modally.
*   **Session Persistence:** Because this is native code, it utilizes the `ConversationManagementStore` to retrieve the stored `conversationUUID`.
    *   As noted in your previous query, this logic ensures that even if the internet drops and the app reconnects, the SDK reuses the existing UUID rather than generating a new one, preventing the "lost session" frustration.

### Summary of the Replacement Strategy
You are essentially building your own custom version of "Mobile Publisher" using the `MessagingUIExample` as the blueprint.

| Feature | Your Current App (Mobile Publisher) | Recommended Approach (`MessagingUIExample`) |
| :--- | :--- | :--- |
| **Site Content** | Experience Cloud (Web) | Experience Cloud loaded in `WKWebView` |
| **Chat Engine** | JavaScript / Web Client | Native iOS SDK (`SMIClientUI`) |
| **Chat Button** | HTML Element | Native Floating Button (`UIButton`) |
| **Connectivity** | Session lost on reload/drop | **Session persists** via local UUID storage |

**Next Steps:**
1.  Open `examples/MessagingUIExample/Views/UIKitMIAW.swift`.
2.  Review lines- to see how the Web View is set up.
3.  Review lines- to see how the native chat button is overlaid on the web content.
4.  Replace the demo URL in `DemoManagementSettings` (or hardcode it in `UIKitMIAW.swift`) with your Experience Cloud URL.

---

Q: When it comes to building a mobile app for Salesforce, I’m aware of the Salesforce Mobile SDK and have always understood that mobile apps are built using that SDK. However, when I look at the sample code provided here, I’m unclear whether it uses the Salesforce Mobile SDK, is built on top of the Mobile SDK, or represents a different or newer approach altogether.

A: Based on the source code provided, the `MessagingUIExample` app **does use the Salesforce Mobile SDK**, but it treats it as a complementary library rather than the sole foundation.

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

---

Q: There are two sample apps, right? Do they both use the Salesforce mobile SDK?

Yes, there are exactly **two** sample apps provided in the repository, but **only one of them** uses the Salesforce Mobile SDK.

Here is the breakdown based on the source code:

### 1. The Two Sample Apps
The repository contains two distinct example projects:
*   **MessagingUIExample (Simple UI SDK Example):** Uses the "UI SDK" (`SMIClientUI`) which provides out-of-the-box user interfaces.
*   **MessagingCoreExample (Simple Core SDK Example):** Uses the "Core SDK" (`SMIClientCore`) which provides data-only APIs, requiring you to build your own UI.

### 2. Salesforce Mobile SDK Usage
They **do not** both use the Salesforce Mobile SDK. Only the `MessagingUIExample` integrates it.

*   **MessagingUIExample (Uses Salesforce Mobile SDK)**
    *   **Evidence:** The project file explicitly lists `SalesforceMobileSDK-iOS-SPM` as a package dependency. It links frameworks such as `SalesforceSDKCore`, `MobileSync`, and `SalesforceAnalytics`.
    *   **Usage:** In the code, it imports `SalesforceSDKCore` to handle authentication. For example, the `GlobalCoreDelegateHandler` uses `AuthHelper.loginIfRequired` to log the user into Salesforce and `RestClient` to fetch authentication tokens,.

*   **MessagingCoreExample (Does NOT use Salesforce Mobile SDK)**
    *   **Evidence:** The project file for this app **only** lists `Swift-Package-InAppMessaging` as a dependency,. It does not include the Salesforce Mobile SDK.
    *   **Usage:** Instead of using the Mobile SDK to fetch user tokens, the code uses hardcoded placeholder strings. In `MessaingViewModel+UserVerifictionDelegate.swift`, the code instructs you to manually fill in `"<YOUR_CUSTOMER_IDENTITY_TOKEN>"`, whereas the UI example fetches this programmatically using the Mobile SDK.

**Summary Table**

| Sample App | Uses MIAW SDK? | Uses Salesforce Mobile SDK? |
| :--- | :--- | :--- |
| **MessagingUIExample** | **Yes** (`SMIClientUI`) | **Yes** (For Auth/Login) |
| **MessagingCoreExample** | **Yes** (`SMIClientCore`) | **No** (Manual Token Handling) |

---

Q: Is MessagingCoreExample complete? Can we really use the app by manually filling in YOUR_CUSTOMER_IDENTITY_TOKEN?

Based on the sources, the short answer is **no**, the **MessagingCoreExample** is not a complete, production-ready application, and manually filling in `YOUR_CUSTOMER_IDENTITY_TOKEN` is not a viable long-term solution for a functioning app.

Here is a detailed breakdown of why this approach is insufficient:

### 1. Hardcoded Tokens Will Expire
While you can technically paste a token string into `MessaingViewModel+UserVerifictionDelegate.swift` to make the app compile and run temporarily, it will break quickly.

*   **The Problem:** The "Customer Identity Token" is typically a JSON Web Token (JWT) which has a set expiration time.
*   **Source Evidence:** The code explicitly includes logic for when a token is `.expired` or needs `.refresh`. In these cases, the code attempts to assign `token = "<YOUR_NEW_CUSTOMER_IDENTITY_TOKEN>"`.
*   **The Consequence:** If you hardcode a static string, your app will be unable to provide a *new* token when the old one expires. A hardcoded token is only useful for a quick test session lasting minutes or hours. For a real app, you must write code to dynamically fetch a new token from an identity provider (as the `MessagingUIExample` does using the Salesforce Mobile SDK).

### 2. Missing User Interface Components
The **MessagingCoreExample** is designed as a developer template to show you how to access data, not how to render it. It deliberately leaves out critical UI elements that your users would expect.

*   **Missing Pre-Chat UI:** The README explicitly warns: "The UI for pre-chat hasn’t been created in this example. If your deployment has pre-chat fields, you must handle that scenario in order to create a conversation".
*   **Manual Message Rendering:** Unlike the UI Example, which gives you a full chat window, this example requires you to manually handle every event type (Text, Images, Rich Links, etc.) in the `ChatFeedList` view. You would have to write the visual code for every single one of these interactions yourself.

### 3. Missing Configuration
Beyond the token, you cannot run the app without updating the configuration file.

*   The `configFile.json` contains "TBD" placeholders for your `OrganizationId`, `DeveloperName`, and `Url`. You must manually replace these with values from your Salesforce Org setup.

### Conclusion
The **MessagingCoreExample** is strictly for developers who want to build a completely custom user interface from scratch and need to see how the underlying data pipeline works.

For your specific goal—replacing a Mobile Publisher app to fix connectivity issues—you should use the **MessagingUIExample**. It handles the token fetching dynamically (using `SalesforceMobileSDK`) and provides a complete, resilient chat interface "out of the box."

---

Q: We do need user authentication. That means we need both SDK, the Mobile SDK is used for Passthrough authentication, right?                                      

Based on the sources, **yes**, your understanding is correct. If you plan to use **Passthrough Verification**, you will need both the **Salesforce Mobile SDK** (for authentication) and the **Messaging for In-App SDK** (for the chat functionality).

Here is the breakdown of how the sample code utilizes the Mobile SDK specifically for Passthrough authentication:

### 1. The Mobile SDK Handles the Login
For Passthrough authentication, the app must first log the user into Salesforce to establish a valid session. The sample app uses the Salesforce Mobile SDK class `AuthHelper` to perform this login.

*   **Source Evidence:** In `GlobalCoreDelegateHandler+UserVerification.swift`, the code explicitly calls `AuthHelper.loginIfRequired` when the authorization method is set to Passthrough [,].

### 2. The Mobile SDK Retrieves the Token
Once logged in, the app needs to retrieve the specific access token required to verify the messaging session. The sample app uses the Mobile SDK's `RestClient` to make an authenticated network request to fetch this token.

*   **Source Evidence:** The function `fetchMIAWJWT` uses `RestClient.shared.send(request: request)` to retrieve the `accessToken` [,].

### 3. The Token is Passed to the Messaging SDK
Finally, that token retrieved by the Mobile SDK is wrapped in a `PassthroughVerification` object and handed over to the Messaging SDK to verify the user.

*   **Source Evidence:** The code initializes the verification object: `PassthroughVerification(jwt: accessToken, ...)` and returns it to the Core Client [].

### Summary
*   **Salesforce Mobile SDK:** Responsible for the "Login via Salesforce" screen, managing the user's session, and fetching the authentication token.
*   **Messaging for In-App SDK:** Uses that token to connect the user to their specific chat history and messaging session.

---

Q: For risk management, my first goal should be to determine whether a native mobile app can effectively address the intermittent connectivity issues, right? This will allow us to decide whether to proceed with building a native mobile app for our Experience Cloud site and migrating all existing users to it.

A: Yes, that is the correct risk management strategy. Before committing development resources to building a custom native iOS app and migrating your user base, you should use the provided sample code to validate that the native SDK actually resolves your specific connectivity pain points.

Because the **MessagingUIExample** is a fully functional application, you can use it as a "Proof of Concept" (PoC) immediately. You can configure it to connect to your specific Salesforce instance and simulate your exact failure scenarios (e.g., toggling airplane mode or killing the app) to verify that the session persists.

Here is the step-by-step plan to validate the solution using the provided sources:

### 1. Configure the Sample App with Your Org Details
You do not need to write new code to test the connectivity logic. You can simply input your Experience Cloud details into the sample app's settings.

*   **Action:** Open the **MessagingUIExample** project.
*   **Locate:** Go to `MIAWConfigurationSettings.swift` or simply run the app and open the **Settings** menu.
*   **Input:** Enter your **Organization Id**, **Developer Name**, and **Domain** (API URL).
*   **Context:** This connects the generic sample app to your specific Salesforce Messaging setup, allowing you to chat with your actual agents or bots.

### 2. Simulate Your "Hybrid" Environment
To ensure the test is realistic, you should test using the "Hybrid" view (Web Content + Native Chat) rather than the pure native SwiftUI view. This mimics your goal of keeping the Experience Cloud site while using native messaging.

*   **Action:** In the running sample app, select **"Custom URL Demo"** from the main menu.
*   **Source Logic:** This view (`UIKitMIAW.swift`) loads a web page in a `WKWebView` while keeping a native chat button floating on top.
*   **Configuration:** By default, this loads `salesforce.com`. You can change this to your Experience Cloud URL in the **UIKit Demo Settings** menu under "Demo Domain", or by modifying the default value in `DemoManagementSettings.swift`.

### 3. Execute the Connectivity "Stress Test"
Once the app is running and connected to your Org, perform the following test to validate the fix for your "dropped session" issue:

1.  **Start a Chat:** Tap the native floating button to launch the `InterfaceViewController`.
2.  **Send a Message:** Establish a session with an agent or bot.
3.  **Simulate Failure:**
    *   **Network Drop:** Turn on Airplane Mode, wait a moment, then turn it off.
    *   **App Kill:** Force close the app completely and relaunch it.
4.  **Verify Persistence:** Re-enter the "Custom URL Demo" and open the chat.
    *   **Success Criteria:** The previous conversation history should appear immediately, and you should be able to continue typing without starting over.

### Why This Validates the Solution
The sample app proves that the native approach solves your issue because of the logic found in `ConversationManagementSettings.swift`.

*   **The Fix:** The sample code explicitly checks `userDefaults` for an existing `conversationId` before creating a new one.
*   **Code Evidence:** `if result.isEmpty { ... return newValue } else { return result }`.
*   **Contrast:** Your current Mobile Publisher app likely resets this ID upon reload or network loss, whereas this native implementation persists the ID locally, ensuring the Salesforce server recognizes the user as the same participant upon reconnection.

If this test passes, you have confirmed that a native wrapper (using the code in `UIKitMIAW.swift`) will solve your connectivity issues while allowing you to keep your Experience Cloud site.


---
Q: Given that user experience is important for our users, who are busy, customer-facing professionals. The target architecture would be a native mobile app that overcomes the connectivity challenges while retaining all the current functionality of the existing mobile publisher app, right? 

A: Based on the sources and your requirements, **yes**, your target architecture is best described as a **Hybrid Native App**.

This approach allows you to build a native mobile app wrapper that solves the connectivity/session issues (using the Native SDKs) while simply loading your existing Experience Cloud site for everything else (retaining all current functionality).

Here is the breakdown of why this specific architecture addresses the needs of your "busy, customer-facing professionals":

### 1. Solving Connectivity Challenges (The Native Layer)
The primary frustration for your users is losing their chat session when the network fluctuates. In your current Mobile Publisher (web-based) app, a page reload or network drop often resets the browser session.

*   **How the Native App fixes this:** By moving the chat feature out of the browser and into the native code, you utilize the **Conversation Management** logic found in the sample code.
*   **Mechanism:** The native app generates a `conversationId` **once** and saves it to the device's local storage (`UserDefaults`).
*   **Source Evidence:** The code in `ConversationManagementSettings.swift` explicitly checks for a stored ID before generating a new one:
    > "We have a chicken & egg situation here where we need to determine when to automatically generate a new conversationId if the old one is reset. If result.isEmpty { ... } else { return result }".
*   **Result:** Even if the busy professional loses signal in an elevator or kills the app, the moment they reopen it, the native app retrieves the *same* ID and restores the conversation history immediately, without starting over.

### 2. Retaining Existing Functionality (The Web Layer)
You do not need to rebuild your portal features, dashboards, or CRM data access natively.

*   **How the Architecture handles this:** The native app acts as a "container." You use a `WKWebView` to load your specific Experience Cloud URL.
*   **Source Evidence:** The file `UIKitMIAW.swift` demonstrates this exact pattern. It initializes a web view and loads the target URL, ensuring your users see the exact same interface they are used to:
    > `let webView = WKWebView()`
    > `webView.load(URLRequest(url: url))`.

### 3. Enhancing the "Busy Professional" UX
The sample code provides features that are superior to the standard web-based chat button, specifically designed for multitasking users.

*   **Floating Native Overlay:** The chat button is a native iOS component (`floatingButton`) that sits *above* the web content. It does not disappear or reload when the user navigates between pages on your Experience Cloud site.
*   **Status Indicators:** Because the chat is native, it can show "Unread Message" counts or "Queue Position" directly on the button/widget without the user having to open the chat window.
    *   *Source Evidence:* The `MessagingSessionWidget` can be configured to show `widgetType: .agentName` which includes an unread counter, or `.queuePosition`, allowing users to monitor their status while working on other tasks.
*   **Background Persistence:** The native client can remain active and listen for events even when the user is not looking at the chat feed, updating the unread count in real-time.

### Summary of Target Architecture
| Component | Implementation in Native App | Benefit |
| :--- | :--- | :--- |
| **Experience Cloud Site** | `WKWebView` loading your Site URL | Retains 100% of current business logic and design. |
| **Messaging Engine** | Native SDK (`SMIClientUI`) | **Solves connectivity issues**; session survives network drops. |
| **Authentication** | Salesforce Mobile SDK (`AuthHelper`) | seamless login that bridges the native app and the web content. |

This hybrid architecture provides the robust connectivity of a native app with the maintenance ease of your existing web site.

---

Q: Now please make a detailed project plan, make sure to list the dependencies for each task. The app is for iOS devices (iPhone and iPad).

A: Based on your requirement to build a native iOS app (iPhone and iPad) that resolves connectivity issues while retaining your Experience Cloud site functionality, here is a detailed project plan.

This plan utilizes the **Hybrid Architecture** found in the `MessagingUIExample` source code.

### Phase 1: Project Initialization & Dependency Management
**Goal:** Establish the native iOS project structure with the required Salesforce SDKs.

*   **Task 1.1: Create iOS Project**
    *   **Action:** Create a new Xcode project supporting iPhone and iPad.
    *   **Configuration:** Set `TARGETED_DEVICE_FAMILY = "1,2"` (iPhone and iPad) as seen in the build settings.
    *   **Minimum Deployment Target:** Set to iOS 16.4 or higher (Source sets it to 16.4).
*   **Task 1.2: Install Dependencies via Swift Package Manager (SPM)**
    *   **Action:** Add the following remote package dependencies to your project:
        1.  **Messaging for In-App (MIAW):** `https://github.com/Salesforce-Async-Messaging/Swift-Package-InAppMessaging.git`.
        2.  **Salesforce Mobile SDK:** `https://github.com/forcedotcom/SalesforceMobileSDK-iOS-SPM`.
    *   **Frameworks to Link:** Ensure `Swift-InAppMessaging`, `SalesforceSDKCore`, and `MobileSync` are linked in your build phases.

### Phase 2: Building the "Hybrid" Container (The Wrapper)
**Goal:** Create the native view that loads your Experience Cloud site while overlaying the native chat interface.

*   **Task 2.1: Implement the Web View**
    *   **Source Reference:** `examples/MessagingUIExample/Views/UIKitMIAW.swift`.
    *   **Action:** Create a `UIViewController` or `UIViewRepresentable` (if using SwiftUI) that initializes a `WKWebView`.
    *   **Dependency:** `WebKit`.
    *   **Logic:** Load your Experience Cloud URL using `webView.load(URLRequest(url: url))`.
*   **Task 2.2: Implement the Native Floating Button**
    *   **Source Reference:** `UIKitMIAW.swift`.
    *   **Action:** Create a `UIButton` (or `RoundedButton` class as shown in source) and add it to the view hierarchy *above* the Web View (`view.addSubview(floatingButton)`).
    *   **Constraint:** Pin the button to the bottom-right corner using Auto Layout anchors.
*   **Task 2.3: Configure Button Action**
    *   **Action:** Link the button tap event to a function that initializes the Messaging UI SDK.

### Phase 3: Connectivity & Session Persistence (The Fix)
**Goal:** Ensure the chat session survives network drops by correctly managing the Conversation UUID.

*   **Task 3.1: Implement UUID Storage Logic**
    *   **Source Reference:** `examples/Shared/Settings/Sections/ConversationManagement/ConversationManagementSettings.swift`.
    *   **Action:** Create a Data Manager class that checks `UserDefaults` for an existing `conversationId` before initializing the client.
    *   **Critical Logic:**
        *   If `conversationId` exists in local storage -> Return it.
        *   If `conversationId` is empty -> Generate `UUID().uuidString`, save it to storage, and return it.
        *   *Note:* This specific task is what solves your "lost session" issue.
*   **Task 3.2: Configure the UI Configuration Object**
    *   **Source Reference:** `MIAWConfigurationStore` and `UIKitMIAW.swift`.
    *   **Action:** Initialize `UIConfiguration` using your Organization ID, Developer Name, and the persistent `conversationId` from Task 3.1.

### Phase 4: Authentication (Bridging Native & Web)
**Goal:** Authenticate the user so they can access the site and chat securely using Passthrough Verification.

*   **Task 4.1: Salesforce Login Implementation**
    *   **Source Reference:** `GlobalCoreDelegateHandler+UserVerification.swift`.
    *   **Dependency:** `SalesforceSDKCore`.
    *   **Action:** Implement `AuthHelper.loginIfRequired`. This will trigger the Salesforce login web flow natively.
*   **Task 4.2: Token Retrieval & Exchange**
    *   **Source Reference:** `GlobalCoreDelegateHandler+UserVerification.swift`.
    *   **Action:** Create a function `fetchMIAWJWT` that uses `RestClient.shared.send(request: request)` to fetch the JWT required for messaging.
*   **Task 4.3: Implement User Verification Delegate**
    *   **Source Reference:** `GlobalCoreDelegateHandler+UserVerification.swift`.
    *   **Action:** Conform to `UserVerificationDelegate`. When the SDK challenges for credentials (`.passthrough`), call your login/token fetch method and return a `PassthroughVerification` object.

### Phase 5: UI/UX Refinement
**Goal:** Ensure a seamless transition between the web content and native chat.

*   **Task 5.1: Hide Experience Cloud Embedded Button**
    *   **Action:** In your Salesforce Experience Cloud Builder, configure the layout to hide the *web-based* Embedded Messaging component when the user is on a mobile device (or use CSS injection via the `WKWebView` to hide it).
    *   **Reason:** Prevents the user from seeing two chat buttons (one web, one native).
*   **Task 5.2: Handle Keyboard Overlays**
    *   **Source Reference:** `WrappedKeyboardDismiss.swift`.
    *   **Action:** Ensure the native chat window handles keyboard dismissal correctly on smaller iPhone screens.

### Phase 6: Testing & Deployment
**Goal:** Validate the connectivity fix.

*   **Task 6.1: Connectivity Stress Test**
    *   **Action:** Run the app, start a chat, force-close the app or toggle Airplane mode, and reopen.
    *   **Success Metric:** The chat history must reload automatically using the ID persisted in Task 3.1 without starting a new session.
*   **Task 6.2: Environment Configuration**
    *   **Source Reference:** `ConnectionEnvironment.swift`.
    *   **Action:** Hardcode your production `OrganizationId` and `DeveloperName` into the release configuration (replacing the debug toggles found in the sample code).

---

Q: Given that we have only one Salesforce Technical Architect, who has not build mobile apps hands-on and can only work on this in part time among mutiple priorities. What's a reasonable timeline? 

Based on the sources provided and your specific constraints (one part-time Salesforce Technical Architect with **no hands-on mobile experience**), a reasonable timeline is **12 to 16 weeks (3–4 months)**.

While the provided sample code (`MessagingUIExample`) significantly accelerates development by providing a working template, the lack of iOS-specific knowledge (Xcode, Swift, Signing, App Store Connect) will be the primary bottleneck.

Here is a realistic schedule breakdown, highlighting the specific source files your Architect should copy to minimize coding effort.

### Phase 1: Environment Setup & Learning Curve (Weeks 1–3)
*   **Goal:** Get the sample app compiling and running on a physical device.
*   **Why this takes time:** Your Architect is likely proficient in Apex/Metadata but new to Xcode, Swift Package Manager (SPM), and Apple's provisioning profiles.
*   **Key Tasks:**
    *   Install Xcode and register for an Apple Developer Account.
    *   Clone the repository and resolve dependencies (`Swift-InAppMessaging` and `SalesforceMobileSDK-iOS-SPM`).
    *   **Critical Shortcut:** Do not start from scratch. Use the `MessagingUIExample` project as the base and strip out the settings menus later.

### Phase 2: Implementing the Hybrid Wrapper (Weeks 4–6)
*   **Goal:** Replace the sample app's default view with your Experience Cloud site.
*   **Why this takes time:** Understanding Swift's `UIViewController` lifecycle vs. SwiftUI.
*   **Source Code Blueprint:**
    *   **Copy Logic:** Your Architect should focus almost exclusively on `UIKitMIAW.swift`. This file already contains the code to load a `WKWebView` and overlay a `floatingButton`.
    *   **Configuration:** Replace the demo URL in `UIKitMIAW.swift` with your Experience Cloud URL.

### Phase 3: Authentication & Session Persistence (Weeks 7–10)
*   **Goal:** Connect Salesforce Login (Mobile SDK) with Messaging (MIAW SDK) and ensure the chat session survives restarts.
*   **Why this takes time:** This involves complex asynchronous code and understanding the interaction between two different SDKs.
*   **Source Code Blueprint:**
    *   **The Persistence Fix:** Implement the logic from `ConversationManagementSettings.swift`. This is the specific code that checks `userDefaults` for an existing ID to prevent lost sessions.
    *   **The Auth Bridge:** Copy the logic from `GlobalCoreDelegateHandler+UserVerification.swift`. Specifically, the `salesforceLogin()` function which uses `AuthHelper.loginIfRequired`, and `fetchMIAWJWT` which retrieves the token.

### Phase 4: UI Refinement & Configuration (Weeks 11–12)
*   **Goal:** Hide the web-chat button and finalize the native look.
*   **Why this takes time:** CSS injection into WebViews or audience targeting in Salesforce to hide the old web button can be trial-and-error.
*   **Source Code Blueprint:**
    *   **Info.plist:** Update `MessagingUIExample--iOS--Info.plist` to handle camera/photo library permissions if you allow file attachments.
    *   **Styling:** Use `Localizable.strings` to change text headers and `Assets.xcassets` to replace the generic Salesforce logo with your company branding.

### Phase 5: Testing & App Store Submission (Weeks 13–16)
*   **Goal:** Beta testing via TestFlight and final release.
*   **Why this takes time:** This is an administrative heavy phase. Creating App Store screenshots, writing privacy manifests (required by Apple), and handling likely rejections regarding "Minimum Functionality" (since it's a wrapper app) often cause delays for first-time mobile submitters.

### Risk Mitigation for your Architect
To stay within this timeline, advise your Architect to:
1.  **Avoid "The Core Example":** Do not let them use `MessagingCoreExample`. It requires building UI from scratch and will double the timeline. Stick to `MessagingUIExample`.
2.  **Focus on `UIKitMIAW.swift`:** This file is essentially the entire app architecture you requested (Web + Native Button).
3.  **Hardcode Configuration:** Instead of building a settings menu like the sample app, they should hardcode your Org ID and URLs into the `MIAWConfigurationStore` logic to save development time.

