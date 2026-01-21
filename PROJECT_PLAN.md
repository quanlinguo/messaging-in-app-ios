# Project Plan: Native iOS App for Experience Cloud with Messaging

## Executive Summary

**Project Goal:** Build a custom native iOS app (Universal - iPhone and iPad) that replaces the current mobile publisher app, solving intermittent connectivity issues while retaining all existing functionality.

**Foundation:** UIKitMIAW.swift from MessagingUIExample
**Target Users:** Busy, customer-facing professionals using Experience Cloud
**Authentication:** Salesforce Passthrough (requires both Messaging SDK and Mobile SDK)
**Timeline:** 6-10 months from start to production
**Platform:** iOS 15.0+, Universal (iPhone and iPad)

---

## Project Phases Overview

| Phase | Duration | Start | Dependencies | Deliverable |
|-------|----------|-------|--------------|-------------|
| **Phase 0: Feature Inventory** | 1-2 weeks | Week 1 | None | Feature requirements document |
| **Phase 1: POC Validation** | 2-4 weeks | Week 3 | Phase 0 | POC validation report, go/no-go decision |
| **Phase 2: Planning & Design** | 2 weeks | Week 7 | Phase 1 approval | Technical design document, sprint plan |
| **Phase 3: MVP Development** | 20-24 weeks | Week 9 | Phase 2 | Production-ready app |
| **Phase 4: Beta Testing** | 3-4 weeks | Week 29 | Phase 3 | Validated app, bug fixes complete |
| **Phase 5: App Store & Migration** | 4-6 weeks | Week 33 | Phase 4 | App in production, users migrated |

**Total Duration:** 32-40 weeks (6-10 months)

---

## Phase 0: Feature Inventory & Requirements (Weeks 1-2)

**Goal:** Document all mobile publisher features and define MVP scope

### Tasks

#### 0.1 Environment Setup (1 day)
**Dependencies:** None
**Assigned to:** iOS Developer
**Deliverables:**
- Development Mac with Xcode 15+
- Apple Developer account access
- Salesforce org access
- Repository cloned and workspace opened

**Steps:**
1. Clone repository: `git clone https://github.com/Salesforce-Async-Messaging/messaging-in-app-ios.git`
2. Open `MessagingExample.xcworkspace`
3. Install Xcode 15 or later
4. Configure code signing with Apple Developer account

#### 0.2 Mobile Publisher Feature Documentation (3 days)
**Dependencies:** 0.1
**Assigned to:** Product Manager + iOS Developer
**Deliverables:** Mobile Publisher Feature Inventory Spreadsheet

**Categories to Document:**
- [ ] Authentication & Identity
- [ ] Push Notifications (types, behaviors, deep links)
- [ ] Offline Capabilities
- [ ] Navigation Structure (tabs, menus, hierarchy)
- [ ] Web View Features (downloads, uploads, link handling)
- [ ] Analytics & Monitoring
- [ ] Security Features
- [ ] User Experience (languages, accessibility, dark mode)
- [ ] App Management (updates, settings)
- [ ] Integrations (third-party SDKs, APIs)

**Method:**
1. Install current mobile publisher app on test device
2. Document every visible feature
3. Test all user flows
4. Capture screenshots of navigation structure
5. Document notification types and behaviors
6. Test offline scenarios
7. Export analytics/monitoring configuration

#### 0.3 Mobile Publisher Behavioral Testing (2 days)
**Dependencies:** 0.2
**Assigned to:** QA + iOS Developer
**Deliverables:** Test Results Document

**Test Scenarios:**
1. **Notification Testing:**
   - Trigger all notification types
   - Document tap behaviors
   - Map deep link destinations
   - Test notification settings

2. **Offline Testing:**
   - Disable network completely
   - Document what works vs fails
   - Test reconnection behavior
   - Identify cached content

3. **Navigation Testing:**
   - Map entire navigation tree
   - Test all menu items
   - Document back/forward behavior
   - Test deep links

4. **File Handling:**
   - Test file uploads (if applicable)
   - Test file downloads
   - Document storage locations
   - Test supported file types

5. **State Persistence:**
   - Background app for 24 hours
   - Force quit and reopen
   - Test session timeout
   - Document state restoration

#### 0.4 User Interviews (2 days)
**Dependencies:** 0.2
**Assigned to:** Product Manager
**Deliverables:** User Needs Analysis Report

**Interview 5-10 active users:**
- Daily usage patterns
- Critical features (must-have)
- Current pain points
- Offline requirements
- Notification preferences
- Feature priorities

**Questions:**
1. What do you use the mobile app for daily?
2. What features would make the app unusable if missing?
3. What happens when you're offline?
4. Do you rely on push notifications? For what?
5. What makes you most productive?
6. What frustrates you about the current app?
7. If you could only keep 3 features, what would they be?

#### 0.5 Feature Prioritization & MVP Definition (1 day)
**Dependencies:** 0.2, 0.3, 0.4
**Assigned to:** Product Manager + Tech Lead
**Deliverables:** MVP Feature List with Priorities

**Create Matrix:**
| Feature | Priority | Complexity | Effort | User Impact | In MVP? |
|---------|----------|------------|--------|-------------|---------|
| ... | Must/Should/Nice | L/M/H | Days | H/M/L | Yes/No/v2 |

**Prioritization Criteria:**
- Must-have: App unusable without this
- Should-have: Users complain if missing
- Nice-to-have: Enhancement for v2

#### 0.6 Requirements Documentation (1 day)
**Dependencies:** 0.5
**Assigned to:** Product Manager
**Deliverables:** Functional Requirements Document

**Contents:**
1. MVP Feature List (detailed specifications)
2. User Stories with acceptance criteria
3. UI/UX requirements
4. Performance requirements
5. Security requirements
6. Compliance requirements
7. iPad-specific requirements (landscape, multitasking)
8. Accessibility requirements
9. Localization requirements (if applicable)

#### 0.7 Effort Estimation & Timeline (1 day)
**Dependencies:** 0.6
**Assigned to:** Tech Lead + iOS Developer
**Deliverables:** Development Estimate Document

**Estimate each feature:**
- Development time
- Testing time
- Risk factors
- Dependencies

**Output:** Realistic timeline (3-4 months, 4-6 months, or 6-9 months)

#### 0.8 Stakeholder Review & Approval (1 day)
**Dependencies:** 0.7
**Assigned to:** Product Manager
**Deliverables:** Signed Project Charter

**Present to stakeholders:**
- Feature inventory
- MVP scope
- Timeline estimate
- Budget requirements
- User migration plan outline

**Decision Gate:** Approve to proceed to POC or adjust scope

---

## Phase 1: POC Validation (Weeks 3-6)

**Goal:** Validate that native SDK solves connectivity issues

### Tasks

#### 1.1 POC Environment Setup (1 day)
**Dependencies:** Phase 0 approval
**Assigned to:** iOS Developer
**Deliverables:** Working MessagingUIExample on test device

**Steps:**
1. Configure `examples/MessagingUIExample/configFile.json`:
   ```json
   {
       "OrganizationId": "YOUR_ORG_ID",
       "DeveloperName": "YOUR_DEPLOYMENT_NAME",
       "Url": "https://YOUR_ORG.salesforce-scrt.com"
   }
   ```
2. Build MessagingUIExample
3. Install on iPhone test device
4. Install on iPad test device
5. Verify both device types work correctly

#### 1.2 UIKitMIAW.swift Configuration (2 days)
**Dependencies:** 1.1
**Assigned to:** iOS Developer
**Deliverables:** UIKitMIAW.swift configured with Experience Cloud URL

**Modifications:**
1. Open `examples/MessagingUIExample/Views/UIKitMIAW.swift`
2. Line 69: Replace demo URL with Experience Cloud URL
3. Test on iPhone (portrait and landscape)
4. Test on iPad (all orientations, multitasking)
5. Configure Passthrough authentication
6. Test Salesforce login flow
7. Verify native messaging launches
8. Verify conversation persistence

**iPad-Specific Testing:**
- Split View compatibility
- Slide Over compatibility
- Landscape layout
- Larger screen layout optimization

#### 1.3 Network Resilience Testing - Automated (3 days)
**Dependencies:** 1.2
**Assigned to:** iOS Developer + QA
**Deliverables:** Test Automation Suite

**Test Scenarios:**

**TS-1: Network Drop During Chat**
- Start conversation with agent
- Enable Airplane mode
- Send message (should queue)
- Disable Airplane mode
- Verify message sends
- Verify conversation continues with same UUID

**TS-2: App Backgrounding**
- Start conversation
- Background app for 30 minutes
- Foreground app
- Verify conversation intact
- Verify UUID persists

**TS-3: Force Quit**
- Start conversation
- Force quit app
- Reopen app
- Verify conversation loads with same UUID

**TS-4: Weak Connectivity**
- Use Network Link Conditioner: 3G
- Start conversation
- Verify graceful degradation
- Switch to Edge network
- Verify messaging still works

**TS-5: Complete Offline**
- Disable all connectivity
- Attempt to send message
- Verify offline indicator
- Verify clear error state
- Enable connectivity
- Verify auto-reconnection

**TS-6: Intermittent Connection**
- Simulate flaky network (on/off every 30 seconds)
- Verify no duplicate messages
- Verify message order preserved
- Verify conversation UUID stable

**Tools:**
- Xcode Network Link Conditioner
- Charles Proxy for network monitoring
- Console logs for SSE reconnection events

**Test on:**
- iPhone 13/14/15 (or latest available)
- iPad (9th gen or later)
- iOS 15.0, 16.0, 17.0, 18.0

#### 1.4 Comparison with Mobile Publisher (2 days)
**Dependencies:** 1.3
**Assigned to:** QA
**Deliverables:** Comparison Report

**Run same test scenarios on mobile publisher app:**
- Document session loss rate
- Document message delivery failures
- Document user experience issues

**Create metrics comparison:**
| Metric | Mobile Publisher | Native SDK | Improvement |
|--------|------------------|------------|-------------|
| Session retention | X% | Y% | +Z% |
| Message delivery | X% | Y% | +Z% |
| Reconnection time | X sec | Y sec | -Z sec |

#### 1.5 User Validation Testing (1 week)
**Dependencies:** 1.3, 1.4
**Assigned to:** Product Manager + QA
**Deliverables:** User Feedback Report

**TestFlight Distribution:**
1. Create TestFlight build of POC
2. Distribute to 5-10 users who experience connectivity issues
3. Provide testing instructions
4. Collect feedback via survey

**Survey Questions:**
- Did you experience session loss? (Never/Rarely/Sometimes/Often)
- Did messages deliver reliably? (Always/Usually/Sometimes/Rarely)
- How was the reconnection experience? (Excellent/Good/Fair/Poor)
- Is this better than the current app? (Much better/Better/Same/Worse)
- Would you switch to this app? (Definitely/Probably/Maybe/No)

**Feedback Collection:**
- Daily check-ins during testing week
- Bug reports via TestFlight
- Session logs for analysis
- Screen recordings of issues

#### 1.6 POC Performance Analysis (2 days)
**Dependencies:** 1.5
**Assigned to:** iOS Developer
**Deliverables:** Performance Report

**Metrics to Measure:**
- Reconnection time (avg, p50, p95)
- Message delivery latency
- App launch time
- Memory usage
- Battery impact
- Network data usage

**Tools:**
- Xcode Instruments (Time Profiler, Allocations, Energy Log)
- Console logs analysis
- TestFlight crash reports

#### 1.7 POC Decision Report (1 day)
**Dependencies:** 1.6
**Assigned to:** Tech Lead + Product Manager
**Deliverables:** POC Validation Report with Go/No-Go Recommendation

**Report Contents:**
1. **Executive Summary**
   - Does native SDK solve connectivity issues? Yes/No
   - Quantified improvement (session retention, message delivery)
   - User feedback summary

2. **Technical Findings**
   - Test results for all scenarios
   - Performance metrics
   - Identified issues and mitigations

3. **Recommendation**
   - Go: Proceed to full development
   - No-Go: Explore alternatives
   - Conditional: Additional POC needed

4. **Next Steps**
   - If Go: Phase 2 planning
   - If No-Go: Alternative options
   - Risks and mitigation strategies

#### 1.8 Stakeholder Decision Gate (1 day)
**Dependencies:** 1.7
**Assigned to:** Product Manager
**Deliverables:** Formal Go/No-Go Decision

**Decision Meeting:**
- Present POC findings
- Review user feedback
- Discuss timeline and budget
- Approve or reject full development

**If Approved:**
- Budget allocation
- Resource commitment
- Timeline approval
- Proceed to Phase 2

**If Rejected:**
- Document decision rationale
- Explore alternatives (per FAQ)
- Project closure

---

## Phase 2: Planning & Technical Design (Weeks 7-8)

**Goal:** Detailed technical architecture and development planning

**Prerequisites:** Phase 1 approved, go decision made

### Tasks

#### 2.1 Technical Architecture Design (3 days)
**Dependencies:** Phase 1 approval
**Assigned to:** Tech Lead + iOS Architect
**Deliverables:** Technical Architecture Document

**Architecture Decisions:**

**App Structure:**
```
CustomExperienceCloudApp (Universal - iPhone/iPad)
│
├── Core (Foundation from UIKitMIAW.swift)
│   ├── WKWebView (Experience Cloud)
│   ├── SMIClientUI (Native Messaging)
│   ├── SMIClientCore (Messaging Core)
│   └── SalesforceSDKCore (Authentication)
│
├── Navigation
│   ├── Tab Bar (if needed) / Side Menu
│   ├── Navigation Controllers
│   └── Deep Link Handler
│
├── Features (To Be Added)
│   ├── Push Notifications
│   ├── Analytics
│   ├── Settings
│   └── Offline Handling
│
└── Shared
    ├── Models
    ├── Services
    ├── Extensions
    └── Utilities
```

**Technology Stack:**
- Language: Swift 5.9+
- UI Framework: SwiftUI + UIKit (hybrid)
- Architecture: MVVM with Combine
- Networking: URLSession + Salesforce Mobile SDK RestClient
- Local Storage: UserDefaults + Keychain
- Analytics: [TBD based on requirements]
- Crash Reporting: [TBD based on requirements]

**iPad Considerations:**
- Universal binary (one app, both iPhone and iPad)
- Adaptive layouts with size classes
- iPad-specific navigation (UISplitViewController for larger screens?)
- Multitasking support (Split View, Slide Over)
- Keyboard shortcuts (if applicable)
- External display support (if applicable)

**Key Decisions to Document:**
1. Navigation pattern (Tab bar vs side menu vs hybrid)
2. State management approach
3. Networking layer design
4. Error handling strategy
5. Logging and debugging approach
6. Dependency injection pattern
7. Testing strategy (unit, integration, UI tests)
8. CI/CD pipeline design

#### 2.2 Data Model & API Design (2 days)
**Dependencies:** 2.1
**Assigned to:** iOS Developer + Backend Lead (if applicable)
**Deliverables:** Data Model Documentation

**Define:**
1. **Local Data Models**
   - User preferences
   - Conversation state
   - Cached content
   - Offline queue

2. **API Contracts**
   - Salesforce APIs used
   - Experience Cloud endpoints
   - Push notification payload format
   - Analytics events

3. **Data Flow**
   - Authentication flow
   - Messaging flow
   - Notification flow
   - Offline sync flow

4. **Persistence Strategy**
   - What goes in UserDefaults
   - What goes in Keychain (secure)
   - What needs Core Data (if anything)
   - Cache invalidation strategy

#### 2.3 UI/UX Design (3 days)
**Dependencies:** 2.1, MVP features from Phase 0
**Assigned to:** UX Designer + iOS Developer
**Deliverables:** UI Design Specifications

**Deliverables:**
1. **Wireframes**
   - All screens (iPhone portrait, iPhone landscape, iPad)
   - Navigation flows
   - State variations (loading, error, empty, success)

2. **High-Fidelity Designs**
   - Brand guidelines applied
   - Color scheme
   - Typography
   - Icon set
   - Spacing and layout

3. **Design System**
   - Reusable components
   - Style guide
   - Accessibility guidelines (WCAG 2.1 AA)
   - Dark mode support

4. **iPad-Specific Designs**
   - Landscape layouts
   - Split View layouts
   - Larger canvas utilization
   - Pointer interaction design

5. **Interaction Design**
   - Transitions and animations
   - Gesture interactions
   - Haptic feedback
   - Loading states

#### 2.4 Security & Compliance Review (2 days)
**Dependencies:** 2.1, 2.2
**Assigned to:** Security Lead + Tech Lead
**Deliverables:** Security Requirements Document

**Review:**
1. **Data Security**
   - Keychain usage for sensitive data
   - Encryption at rest
   - Secure communication (TLS)
   - Certificate pinning requirements

2. **Authentication Security**
   - OAuth flow security
   - Token storage and refresh
   - Biometric authentication (if required)
   - Session timeout

3. **Compliance**
   - GDPR (if applicable)
   - HIPAA (if applicable)
   - SOC 2 (if applicable)
   - Industry-specific requirements

4. **App Security**
   - Code obfuscation (if needed)
   - Jailbreak detection (if needed)
   - Screenshot prevention (for sensitive screens)
   - SSL pinning

5. **Third-Party SDKs**
   - Review all dependencies
   - License compliance
   - Security audit of dependencies
   - Update policy

#### 2.5 Development Environment Setup (2 days)
**Dependencies:** 2.1
**Assigned to:** iOS Developer + DevOps
**Deliverables:** Complete Development Environment

**Setup:**
1. **Repository Structure**
   ```
   CustomExperienceCloudApp/
   ├── CustomExperienceCloudApp/      # Main app target
   ├── CustomExperienceCloudAppTests/ # Unit tests
   ├── CustomExperienceCloudAppUITests/ # UI tests
   ├── Shared/                         # Shared code (from examples)
   ├── Podfile or Package.swift        # Dependencies
   └── README.md
   ```

2. **Xcode Project Configuration**
   - Create new Xcode project
   - Configure build settings
   - Set minimum iOS version (15.0+)
   - Configure code signing
   - Set bundle identifier
   - Configure entitlements (push notifications, keychain)

3. **Dependency Management**
   - Add SMIClientCore via SPM
   - Add SMIClientUI via SPM
   - Add SalesforceMobileSDK-iOS-SPM via SPM
   - Add analytics SDK (if chosen)
   - Add crash reporting SDK (if chosen)

4. **CI/CD Pipeline**
   - GitHub Actions / Bitrise / Fastlane
   - Automated builds on PR
   - Unit test execution
   - UI test execution
   - TestFlight distribution for beta
   - Code coverage reporting
   - Linting (SwiftLint)

5. **Code Quality Tools**
   - SwiftLint configuration
   - SwiftFormat configuration
   - SonarQube (if applicable)
   - Danger (for PR automation)

#### 2.6 Import UIKitMIAW.swift Foundation (1 day)
**Dependencies:** 2.5
**Assigned to:** iOS Developer
**Deliverables:** UIKitMIAW.swift integrated into new project

**Steps:**
1. Copy `UIKitMIAW.swift` to new project
2. Copy relevant files from `Shared/` folder:
   - `GlobalCoreDelegateHandler` and extensions
   - `ConversationManagementStore`
   - `MIAWConfigurationStore`
   - Settings infrastructure (if using)
3. Update imports and namespaces
4. Configure with Experience Cloud URL
5. Test basic flow works
6. Test on both iPhone and iPad

#### 2.7 Sprint Planning (2 days)
**Dependencies:** 2.1, 2.2, 2.3, MVP features from Phase 0
**Assigned to:** Tech Lead + Team
**Deliverables:** Sprint Plan for Phase 3

**Create Sprint Breakdown:**
- Sprint 0: Foundation & Core Setup (2 weeks)
- Sprint 1-2: Push Notifications (4 weeks)
- Sprint 3-4: Navigation & UI (4 weeks)
- Sprint 5-6: Settings & Preferences (4 weeks)
- Sprint 7-8: Analytics & Monitoring (4 weeks)
- Sprint 9-10: Testing & Bug Fixes (4 weeks)

**For Each Sprint:**
- User stories with acceptance criteria
- Story points estimation
- Definition of done
- Dependencies
- Risk assessment

#### 2.8 Risk Assessment & Mitigation (1 day)
**Dependencies:** 2.7
**Assigned to:** Tech Lead + Product Manager
**Deliverables:** Risk Register

**Identify Risks:**
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| App Store rejection | Medium | High | Early review of guidelines, pre-submission checklist |
| Performance issues on iPad | Low | Medium | Performance testing throughout development |
| Third-party SDK bugs | Medium | Medium | Version pinning, escape hatches |
| Timeline overrun | Medium | High | Buffer time in schedule, prioritization |
| User adoption issues | Medium | High | Beta testing, user training, communication |

#### 2.9 Resource Allocation (1 day)
**Dependencies:** 2.7
**Assigned to:** Project Manager
**Deliverables:** Resource Plan

**Team Structure:**
- iOS Developer (Lead) - Full time
- iOS Developer (Junior) - Full time
- QA Engineer - Full time
- UX Designer - 50% time
- Backend Support (if needed) - As needed
- Product Manager - 25% time
- Project Manager - 25% time

**Key Milestones:**
- Sprint 0 complete: Week 9
- Push notifications working: Week 13
- Navigation complete: Week 17
- Beta-ready: Week 29
- App Store submission: Week 33

#### 2.10 Phase 2 Review & Approval (1 day)
**Dependencies:** All Phase 2 tasks
**Assigned to:** Product Manager
**Deliverables:** Approved Technical Design

**Review Meeting:**
- Present technical architecture
- Review sprint plan
- Review resource allocation
- Review risk mitigation
- Approve to begin development

---

## Phase 3: MVP Development (Weeks 9-32)

**Goal:** Build production-ready iOS app (Universal for iPhone and iPad)

### Sprint 0: Foundation & Core Setup (Weeks 9-10)

#### 3.0.1 Project Scaffold (2 days)
**Dependencies:** Phase 2 approval
**Assigned to:** iOS Lead
**Deliverables:** Clean app builds and runs

**Tasks:**
- Set up project structure per 2.5
- Configure all targets (app, tests, UI tests)
- Set up dependency management
- Verify builds on both iPhone and iPad simulators

#### 3.0.2 CI/CD Pipeline Implementation (3 days)
**Dependencies:** 3.0.1
**Assigned to:** iOS Lead + DevOps
**Deliverables:** Automated build pipeline

**Pipeline Stages:**
1. Code checkout
2. Dependency installation
3. Linting (SwiftLint)
4. Unit tests
5. UI tests (on simulator)
6. Build app
7. Archive for TestFlight (on main branch)
8. Upload symbols for crash reporting

**Triggers:**
- Pull request opened: Lint + build + test
- Merge to develop: Full pipeline + TestFlight beta
- Merge to main: Full pipeline + TestFlight production

#### 3.0.3 Core Integration (3 days)
**Dependencies:** 3.0.1
**Assigned to:** iOS Developer
**Deliverables:** UIKitMIAW.swift foundation working

**Integration Tasks:**
1. Import UIKitMIAW.swift and dependencies
2. Configure Experience Cloud URL
3. Configure Salesforce credentials
4. Set up Passthrough authentication
5. Test messaging flow on iPhone
6. Test messaging flow on iPad
7. Verify conversation persistence
8. Set up logging and debugging

#### 3.0.4 Basic Navigation Shell (2 days)
**Dependencies:** 3.0.3, UI designs from 2.3
**Assigned to:** iOS Developer
**Deliverables:** App navigation structure

**Based on MVP requirements, implement:**
- Tab bar (if needed) or navigation container
- Main container for Experience Cloud web view
- Messaging view integration
- Settings placeholder
- Handle iPhone vs iPad navigation differences

**iPad Specific:**
- Decide on navigation pattern for iPad (tabs vs split view)
- Implement adaptive layout
- Test all orientations

#### 3.0.5 Branding & Assets (2 days)
**Dependencies:** UI designs from 2.3
**Assigned to:** iOS Developer + Designer
**Deliverables:** App branded

**Assets:**
1. App icon (all required sizes, iPhone and iPad)
2. Launch screen (adaptive for all devices)
3. Color assets (light mode and dark mode)
4. Custom fonts (if applicable)
5. Image assets
6. Localization setup (if needed)

**Configuration:**
- Set app name
- Set bundle identifier
- Configure app version and build number
- Set up Info.plist with required keys

#### 3.0.6 Analytics & Crash Reporting Setup (2 days)
**Dependencies:** 3.0.1
**Assigned to:** iOS Developer
**Deliverables:** Analytics and crash reporting working

**Implementation:**
- Integrate chosen analytics SDK
- Set up crash reporting (Crashlytics, Sentry, etc.)
- Configure basic events (app launch, screen views)
- Test crash reporting works
- Set up dashboards

#### 3.0.7 Unit Testing Framework (1 day)
**Dependencies:** 3.0.3
**Assigned to:** iOS Developer
**Deliverables:** Unit test infrastructure

**Setup:**
- Test target configured
- XCTest framework
- Mock framework (if using - e.g., Mockingbird)
- Test utilities and helpers
- Example tests for core components

#### 3.0.8 Sprint 0 Review (1 day)
**Dependencies:** All Sprint 0 tasks
**Assigned to:** Team
**Deliverables:** Sprint 0 retrospective

**Review:**
- Demo working foundation
- Verify all infrastructure working
- Identify blockers for Sprint 1
- Adjust plan if needed

---

### Sprint 1-2: Push Notifications (Weeks 11-14)

**Goal:** Implement push notifications with deep linking

#### 3.1.1 APNs Setup (1 day)
**Dependencies:** 3.0.1
**Assigned to:** iOS Lead
**Deliverables:** APNs configured

**Tasks:**
1. Create APNs certificate/key in Apple Developer
2. Configure in Salesforce org
3. Enable Push Notifications capability in Xcode
4. Test certificate with test notification

#### 3.1.2 Push Notification Registration (2 days)
**Dependencies:** 3.1.1
**Assigned to:** iOS Developer
**Deliverables:** Device registers for notifications

**Implementation:**
```swift
import UserNotifications

// Request authorization
UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound])

// Register with APNs
UIApplication.shared.registerForRemoteNotifications()

// Handle device token
func application(_ application: UIApplication,
                 didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    // Send token to Salesforce
}
```

**Test:**
- User grants permission flow
- User denies permission flow
- Token sent to Salesforce backend
- Test on both iPhone and iPad

#### 3.1.3 Notification Handling (3 days)
**Dependencies:** 3.1.2
**Assigned to:** iOS Developer
**Deliverables:** App handles notifications

**Implementation:**
- Foreground notification handling (banner)
- Background notification handling
- Notification tap handling
- Badge count management
- Notification categories and actions (if needed)

**Notification Types to Support:**
1. New message from agent
2. Agent joined conversation
3. Conversation ended
4. [Other types from feature inventory]

#### 3.1.4 Deep Linking (3 days)
**Dependencies:** 3.1.3
**Assigned to:** iOS Developer
**Deliverables:** Deep links navigate correctly

**Implementation:**
1. **URL Scheme Setup**
   - Register custom URL scheme
   - Handle URL in AppDelegate/SceneDelegate

2. **Universal Links Setup**
   - Configure Associated Domains
   - Create apple-app-site-association file
   - Host file on web server

3. **Deep Link Router**
   ```swift
   enum DeepLink {
       case conversation(UUID)
       case settings
       case experienceCloud(URL)
   }

   class DeepLinkRouter {
       func route(_ deepLink: DeepLink) {
           // Navigate to appropriate screen
       }
   }
   ```

4. **Notification Payload Parsing**
   - Extract conversation ID from payload
   - Navigate to conversation on tap

**Test Scenarios:**
- Tap notification when app is closed → Opens to conversation
- Tap notification when app is in background → Switches to conversation
- Tap notification when app is in foreground → Shows conversation
- Test on both iPhone and iPad

#### 3.1.5 Notification Settings UI (2 days)
**Dependencies:** 3.1.2
**Assigned to:** iOS Developer
**Deliverables:** User can manage notification preferences

**Implementation:**
- Settings screen with notification toggles
- Link to system settings
- Sync preferences with backend (if applicable)
- Test preferences persist

#### 3.1.6 Push Notification Testing (3 days)
**Dependencies:** 3.1.3, 3.1.4
**Assigned to:** QA + iOS Developer
**Deliverables:** All notification scenarios tested

**Test Matrix:**
| Scenario | iPhone | iPad | Notes |
|----------|--------|------|-------|
| Receive notification (foreground) | ✓ | ✓ | |
| Receive notification (background) | ✓ | ✓ | |
| Receive notification (app closed) | ✓ | ✓ | |
| Tap notification → deep link | ✓ | ✓ | |
| Multiple notifications | ✓ | ✓ | Badge count |
| Notification actions (if any) | ✓ | ✓ | |
| Silent notifications | ✓ | ✓ | |

**Tools:**
- Test notifications via Salesforce
- Test notifications via APNs testing tool
- Test on physical devices (required for push)

#### 3.1.7 Sprint 1-2 Review (1 day)
**Dependencies:** All Sprint 1-2 tasks
**Assigned to:** Team
**Deliverables:** Sprint review and demo

**Demo:**
- Send test notification from Salesforce
- Show notification received on iPhone
- Show notification received on iPad
- Tap notification and navigate to conversation
- Show badge count updates

---

### Sprint 3-4: Navigation & UI Parity (Weeks 15-18)

**Goal:** Replicate mobile publisher navigation and UI structure

#### 3.2.1 Navigation Structure Implementation (5 days)
**Dependencies:** Feature inventory (Phase 0.2), UI designs (2.3)
**Assigned to:** iOS Developer
**Deliverables:** Full navigation matching mobile publisher

**Based on mobile publisher structure, implement:**

**Option A: Tab Bar Navigation**
```swift
UITabBarController with tabs:
- Tab 1: Experience Cloud (WKWebView)
- Tab 2: Messages (Native Messaging)
- Tab 3: Settings
```

**Option B: Side Menu Navigation**
```swift
Drawer menu with options:
- Home (Experience Cloud)
- Messages
- Profile
- Settings
```

**Option C: Hybrid**
```swift
Tab bar on iPhone
Split View on iPad
```

**iPad Considerations:**
- Use UISplitViewController for iPad
- Master-detail pattern
- Adaptive layouts for different size classes

**Implementation:**
1. Create tab bar or menu container
2. Integrate Experience Cloud web view
3. Integrate messaging interface
4. Add settings screen
5. Handle state restoration
6. Test navigation flows

#### 3.2.2 Experience Cloud Web View Enhancement (3 days)
**Dependencies:** 3.2.1
**Assigned to:** iOS Developer
**Deliverables:** Enhanced web view with mobile publisher parity

**Enhancements:**
1. **JavaScript Bridge** (if needed)
   ```swift
   // Allow JavaScript to call native code
   webView.configuration.userContentController.add(self, name: "nativeHandler")
   ```

2. **Cookie Management**
   - Share cookies between Salesforce auth and web view
   - Persist cookies across app launches

3. **Link Handling**
   - Internal links: Stay in web view
   - External links: Open in Safari (or custom behavior)
   - Download links: Handle appropriately

4. **Progress Indicator**
   - Show loading progress
   - Handle errors gracefully

5. **Pull to Refresh**
   - Implement if mobile publisher has this

6. **Navigation Controls**
   - Back/Forward buttons (if needed)
   - Refresh button
   - Share/Copy URL (if needed)

**iPad Specific:**
- Optimize for larger screen
- Better utilize space
- Test in Split View and Slide Over

#### 3.2.3 Hide Web Chat Button (1 day)
**Dependencies:** 3.2.2
**Assigned to:** iOS Developer + Backend Support
**Deliverables:** Web chat button hidden in native app

**Approaches:**

**Option 1: User Agent Detection**
```swift
// Set custom user agent
let userAgent = "CustomNativeApp/1.0"
webView.customUserAgent = userAgent

// In Experience Cloud, detect and hide chat button:
// if (navigator.userAgent.includes('CustomNativeApp')) {
//     hideEmbeddedChat();
// }
```

**Option 2: URL Parameter**
```swift
// Add parameter to URL
let url = URL(string: "https://your-site.force.com?nativeApp=true")

// In Experience Cloud, detect parameter and hide
```

**Option 3: JavaScript Injection**
```swift
let hideChat = """
    document.querySelector('.embedded-chat').style.display = 'none';
"""
webView.evaluateJavaScript(hideChat)
```

**Coordinate with Experience Cloud admin** to implement

#### 3.2.4 Custom UI Components (3 days)
**Dependencies:** UI designs (2.3)
**Assigned to:** iOS Developer
**Deliverables:** Reusable UI components

**Components:**
- Custom buttons matching design
- Loading indicators
- Error views
- Empty states
- Pull to refresh (if not using default)
- Custom navigation bar (if needed)
- Alerts and action sheets (consistent style)

**Accessibility:**
- VoiceOver labels
- Dynamic Type support
- Sufficient color contrast
- Accessible interactions

#### 3.2.5 Dark Mode Support (2 days)
**Dependencies:** 3.2.4
**Assigned to:** iOS Developer
**Deliverables:** App supports dark mode

**Implementation:**
1. Define color assets for light and dark modes
2. Use semantic colors throughout app
3. Test all screens in dark mode
4. Handle web view in dark mode (if applicable)
5. Test on iPhone and iPad

#### 3.2.6 iPad-Specific Layout Optimization (3 days)
**Dependencies:** 3.2.1, 3.2.2
**Assigned to:** iOS Developer
**Deliverables:** Optimized iPad experience

**Optimizations:**
1. **Split View**
   - Master: Navigation or menu
   - Detail: Experience Cloud web view or messaging

2. **Multitasking**
   - Test in Split View (1/2, 1/3, 2/3)
   - Test in Slide Over
   - Test in Picture in Picture (if applicable)

3. **Landscape Layout**
   - Optimize for landscape orientation
   - Better space utilization

4. **External Display**
   - Handle external display if needed
   - Test with iPad connected to monitor

5. **Keyboard Support**
   - Keyboard shortcuts (if applicable)
   - Hardware keyboard support
   - Test with Magic Keyboard

6. **Pointer Support**
   - Hover states for cursor
   - Pointer interactions

#### 3.2.7 Responsive Design Testing (2 days)
**Dependencies:** 3.2.6
**Assigned to:** QA
**Deliverables:** All device sizes tested

**Test Matrix:**

**iPhone:**
- iPhone SE (small screen)
- iPhone 15 Pro (standard)
- iPhone 15 Pro Max (large)
- Portrait and landscape

**iPad:**
- iPad (10.2")
- iPad Air (10.9")
- iPad Pro 11"
- iPad Pro 12.9"
- All orientations
- Split View scenarios
- Slide Over scenarios

**Verification:**
- Layouts adapt correctly
- Text is readable
- Touch targets are accessible (min 44pt)
- No content clipped or truncated

#### 3.2.8 Sprint 3-4 Review (1 day)
**Dependencies:** All Sprint 3-4 tasks
**Assigned to:** Team
**Deliverables:** Sprint review and demo

**Demo:**
- Show navigation on iPhone
- Show navigation on iPad
- Demonstrate web view functionality
- Show dark mode
- Demonstrate iPad-specific features

---

### Sprint 5-6: Settings, Preferences & User Experience (Weeks 19-22)

**Goal:** Settings UI, preferences, and UX polish

#### 3.3.1 Settings UI Implementation (4 days)
**Dependencies:** UI designs (2.3), navigation (3.2.1)
**Assigned to:** iOS Developer
**Deliverables:** Complete settings screen

**Settings Sections:**

1. **Account**
   - User profile information
   - Logged in as: [user@email.com]
   - Logout button
   - Switch account (if multi-org)

2. **Notifications**
   - Enable/disable notifications
   - Notification types toggles
   - Badge count toggle
   - Sound toggle
   - Link to system settings

3. **Appearance**
   - Dark mode (System / Light / Dark)
   - Text size (if custom)

4. **Data & Privacy**
   - Clear cache
   - Clear conversation history (confirm dialog)
   - Data usage statistics
   - Privacy policy link
   - Terms of service link

5. **About**
   - App version
   - Build number
   - SDK versions
   - Open source licenses
   - Help/Support link

**Implementation:**
- Use SwiftUI List/Form for settings
- Persist preferences to UserDefaults
- Apply preferences immediately
- Test on iPhone and iPad

#### 3.3.2 User Preferences Management (2 days)
**Dependencies:** 3.3.1
**Assigned to:** iOS Developer
**Deliverables:** Preferences persist and sync

**Implementation:**
```swift
class UserPreferences: ObservableObject {
    @AppStorage("notificationsEnabled") var notificationsEnabled = true
    @AppStorage("darkMode") var darkMode: String = "system"
    @AppStorage("textSize") var textSize: String = "medium"

    // Sync with backend if needed
    func sync() async {
        // Upload preferences to Salesforce
    }
}
```

**Features:**
- Local persistence (UserDefaults)
- Backend sync (if applicable)
- Default values
- Migration for preference changes

#### 3.3.3 Logout & Account Management (2 days)
**Dependencies:** 3.3.1
**Assigned to:** iOS Developer
**Deliverables:** Logout flow complete

**Implementation:**
1. **Logout Button**
   - Show confirmation alert
   - Clear conversation data
   - Clear cached content
   - Clear Keychain items
   - Call Salesforce Mobile SDK logout
   - Return to login screen

2. **Token Refresh Handling**
   - Automatic token refresh in background
   - Handle expired token gracefully
   - Re-authenticate user if needed

3. **Session Timeout**
   - Implement timeout per mobile publisher
   - Warn user before timeout
   - Auto-logout after timeout

#### 3.3.4 Loading States & Error Handling (3 days)
**Dependencies:** 3.2.1
**Assigned to:** iOS Developer
**Deliverables:** Consistent loading and error UX

**Loading States:**
1. **Initial App Launch**
   - Splash screen
   - Loading indicator during initialization

2. **Web View Loading**
   - Progress bar or spinner
   - Skeleton screens (if applicable)

3. **Messaging Loading**
   - Loading previous messages
   - Sending message indicator

4. **Network Operations**
   - Generic loading overlay (with cancel option if long)

**Error Handling:**
1. **Network Errors**
   - Clear error message
   - Retry button
   - Offline indicator

2. **Authentication Errors**
   - Expired session → prompt re-login
   - Invalid credentials → error message

3. **Messaging Errors**
   - Failed to send message → show retry
   - Failed to load conversation → error state with retry

4. **Web View Errors**
   - Failed to load page → error view with reload button

**Implementation:**
- Consistent error messages
- User-friendly language (no technical jargon)
- Actionable (Retry, Cancel, Contact Support)
- Accessibility support

#### 3.3.5 Offline Handling (3 days)
**Dependencies:** Feature requirements from Phase 0
**Assigned to:** iOS Developer
**Deliverables:** Graceful offline experience

**Implementation:**

1. **Connectivity Monitoring**
   ```swift
   import Network

   let monitor = NWPathMonitor()
   monitor.pathUpdateHandler = { path in
       if path.status == .satisfied {
           // Online
       } else {
           // Offline
       }
   }
   ```

2. **Offline Indicator**
   - Banner at top of screen
   - "You're offline" message
   - Auto-hide when back online

3. **Offline Behavior**
   - Experience Cloud: Show cached version or offline message
   - Messaging: Queue messages for sending when online
   - Settings: Still accessible
   - Show what's available offline

4. **Reconnection**
   - Auto-reconnect when network available
   - Sync queued messages
   - Refresh stale data

#### 3.3.6 Accessibility Implementation (2 days)
**Dependencies:** All UI implementation
**Assigned to:** iOS Developer
**Deliverables:** WCAG 2.1 AA compliant

**Accessibility Features:**
1. **VoiceOver**
   - All UI elements have labels
   - Meaningful labels (not "Button")
   - Logical focus order
   - Grouped related elements

2. **Dynamic Type**
   - Support system text size
   - Layouts adapt to larger text
   - Test at accessibility sizes

3. **Color Contrast**
   - Minimum 4.5:1 for normal text
   - Minimum 3:1 for large text
   - Test with color contrast tools

4. **Touch Targets**
   - Minimum 44x44 points
   - Adequate spacing between targets

5. **Reduce Motion**
   - Respect reduce motion preference
   - Disable animations if set

6. **Voice Control**
   - Test navigation with voice control

**Testing:**
- Enable VoiceOver and test all flows
- Test with large text sizes
- Test with high contrast mode
- Test with reduce motion enabled

#### 3.3.7 Localization Setup (2 days, if required)
**Dependencies:** Feature requirements
**Assigned to:** iOS Developer
**Deliverables:** App supports required languages

**If localization required:**
1. Extract all strings to Localizable.strings
2. Use NSLocalizedString throughout
3. Support RTL languages (if needed)
4. Localize dates, numbers, currencies
5. Test in all supported languages

**Languages to support:** [Per requirements from Phase 0]

#### 3.3.8 Sprint 5-6 Review (1 day)
**Dependencies:** All Sprint 5-6 tasks
**Assigned to:** Team
**Deliverables:** Sprint review and demo

**Demo:**
- Show settings screen
- Demonstrate logout flow
- Show loading states
- Demonstrate error handling
- Show offline mode
- Demonstrate accessibility features

---

### Sprint 7-8: Analytics, Monitoring & Performance (Weeks 23-26)

**Goal:** Production-ready monitoring and performance optimization

#### 3.4.1 Analytics Implementation (3 days)
**Dependencies:** Analytics SDK setup (3.0.6)
**Assigned to:** iOS Developer
**Deliverables:** Comprehensive analytics tracking

**Events to Track:**

1. **User Actions**
   - App launched
   - User logged in
   - User logged out
   - Screen viewed (all screens)
   - Button tapped (key actions)
   - Notification received
   - Notification tapped
   - Message sent
   - Message received
   - Conversation started
   - Conversation ended
   - Settings changed

2. **System Events**
   - App backgrounded
   - App foregrounded
   - App crashed (via crash reporting)
   - Network connectivity changed
   - Token refreshed
   - Error occurred

3. **Business Metrics**
   - Daily active users
   - Session duration
   - Messages sent per session
   - Conversation completion rate
   - Time to first message
   - Response time

**Implementation:**
```swift
class AnalyticsService {
    static let shared = AnalyticsService()

    func track(event: String, properties: [String: Any]? = nil) {
        // Send to analytics backend
        AnalyticsSDK.track(event, properties: properties)
    }

    func screen(name: String, properties: [String: Any]? = nil) {
        // Track screen view
        AnalyticsSDK.screen(name, properties: properties)
    }

    func identify(userId: String, traits: [String: Any]? = nil) {
        // Identify user
        AnalyticsSDK.identify(userId, traits: traits)
    }
}
```

**Privacy:**
- Comply with privacy regulations
- Get user consent if required
- Anonymize PII
- Allow opt-out

#### 3.4.2 Error Tracking & Logging (2 days)
**Dependencies:** Crash reporting setup (3.0.6)
**Assigned to:** iOS Developer
**Deliverables:** Comprehensive error tracking

**Implementation:**

1. **Crash Reporting**
   - Already set up in Sprint 0
   - Verify all crashes captured
   - Test crash reports sent
   - Set up alerts for critical crashes

2. **Error Logging**
   ```swift
   class Logger {
       enum Level {
           case debug, info, warning, error, critical
       }

       static func log(_ message: String, level: Level,
                      file: String = #file, function: String = #function,
                      line: Int = #line) {
           // Log to console
           print("[\(level)] \(file):\(line) \(function) - \(message)")

           // Send to remote logging service (for error and critical)
           if level == .error || level == .critical {
               ErrorTrackingSDK.log(message, metadata: [
                   "file": file,
                   "function": function,
                   "line": line
               ])
           }
       }
   }
   ```

3. **Non-Fatal Error Tracking**
   - API failures
   - Unexpected states
   - Validation errors
   - Background task failures

4. **Breadcrumbs**
   - User actions leading to error
   - State information
   - Network requests
   - Helps debug issues

#### 3.4.3 Performance Monitoring (3 days)
**Dependencies:** 3.4.1
**Assigned to:** iOS Developer
**Deliverables:** Performance metrics tracked

**Metrics to Monitor:**

1. **App Performance**
   - App launch time (cold start, warm start)
   - Time to interactive
   - Screen render time
   - Memory usage
   - CPU usage
   - Battery impact

2. **Network Performance**
   - API response times
   - Successful vs failed requests
   - Download sizes
   - Upload sizes

3. **User Experience**
   - Time to first message
   - Message send latency
   - Conversation load time
   - Web view load time

**Implementation:**
```swift
class PerformanceMonitor {
    static func measureAppLaunch() {
        let launchTime = ProcessInfo.processInfo.systemUptime
        AnalyticsService.shared.track("app_launch_time", properties: [
            "duration": launchTime
        ])
    }

    static func measureOperation<T>(_ name: String, operation: () -> T) -> T {
        let start = Date()
        let result = operation()
        let duration = Date().timeIntervalSince(start)

        AnalyticsService.shared.track("operation_duration", properties: [
            "operation": name,
            "duration": duration
        ])

        return result
    }
}
```

#### 3.4.4 Performance Optimization - App Launch (2 days)
**Dependencies:** 3.4.3
**Assigned to:** iOS Developer
**Deliverables:** Optimized app launch time

**Optimizations:**
1. Defer non-critical initialization
2. Lazy load dependencies
3. Optimize SDK initialization
4. Reduce main thread work
5. Use caching where appropriate

**Target:** App launch < 2 seconds to first screen

**Measure:**
- Use Instruments (Time Profiler)
- Test on older devices (iPhone SE)
- Test on iPad

#### 3.4.5 Performance Optimization - Memory (2 days)
**Dependencies:** 3.4.3
**Assigned to:** iOS Developer
**Deliverables:** Optimized memory usage

**Optimizations:**
1. Fix memory leaks (Instruments - Leaks)
2. Optimize image caching
3. Release unused resources
4. Limit conversation history in memory
5. Clear web view cache periodically

**Target:** Memory usage < 150MB under normal use

**Test:**
- Long-running sessions (hours)
- Multiple conversations
- Large messages with images
- Background/foreground cycles

#### 3.4.6 Performance Optimization - Battery (1 day)
**Dependencies:** 3.4.3
**Assigned to:** iOS Developer
**Deliverables:** Optimized battery usage

**Optimizations:**
1. Efficient background processing
2. Minimize network requests
3. Batch API calls
4. Reduce location updates (if any)
5. Optimize polling intervals

**Measure:**
- Use Instruments (Energy Log)
- Test on physical device
- Simulate long session (4+ hours)

#### 3.4.7 Performance Testing on Devices (2 days)
**Dependencies:** 3.4.4, 3.4.5, 3.4.6
**Assigned to:** QA
**Deliverables:** Performance test results

**Test Matrix:**

| Device | App Launch | Memory | Battery | Frame Rate | Notes |
|--------|------------|--------|---------|------------|-------|
| iPhone SE | < 2s | < 150MB | Good | 60fps | Minimum spec |
| iPhone 15 | < 1s | < 150MB | Excellent | 60fps | Current gen |
| iPhone 15 Pro Max | < 1s | < 150MB | Excellent | 60fps | High end |
| iPad (10.2") | < 2s | < 200MB | Good | 60fps | Entry iPad |
| iPad Pro 12.9" | < 1s | < 200MB | Excellent | 120fps | High end |

**Scenarios:**
- Fresh install (cold launch)
- Warm launch (app backgrounded)
- Long session (4 hours)
- Multiple conversations
- Large message history
- Poor network conditions

#### 3.4.8 Sprint 7-8 Review (1 day)
**Dependencies:** All Sprint 7-8 tasks
**Assigned to:** Team
**Deliverables:** Sprint review

**Review:**
- Analytics dashboard showing real data
- Performance metrics review
- Crash-free rate
- Memory/battery benchmarks

---

### Sprint 9-10: Integration Testing & Bug Fixes (Weeks 27-30)

**Goal:** Comprehensive testing and stability

#### 3.5.1 Integration Test Suite (5 days)
**Dependencies:** All feature implementation complete
**Assigned to:** iOS Developer + QA
**Deliverables:** Automated integration tests

**Test Scenarios:**

1. **Authentication Flow**
   - First launch → login
   - Login success → main screen
   - Login failure → error
   - Token refresh
   - Logout → login screen

2. **Messaging Flow**
   - Start conversation
   - Send text message
   - Send image
   - Receive message
   - Receive notification
   - Tap notification → conversation
   - End conversation

3. **Network Resilience**
   - Send message offline → queues
   - Go online → sends
   - Disconnect mid-conversation → persists
   - Reconnect → continues

4. **Navigation Flow**
   - All navigation paths
   - Deep links
   - State restoration
   - Background/foreground

5. **Settings Flow**
   - Change preferences
   - Preferences persist
   - Logout
   - Clear cache

**Implementation:**
- XCUITest for UI automation
- Integration with CI/CD
- Run on every PR
- Test on simulator and device

#### 3.5.2 End-to-End Testing (3 days)
**Dependencies:** 3.5.1
**Assigned to:** QA
**Deliverables:** E2E test results

**User Journeys:**

**Journey 1: New User**
1. Install app
2. First launch
3. Login with Salesforce
4. Browse Experience Cloud
5. Tap native chat button
6. Start conversation with agent
7. Send message
8. Receive response
9. Background app
10. Receive push notification
11. Tap notification
12. Return to conversation
13. End conversation

**Journey 2: Returning User**
1. Launch app (already logged in)
2. Navigate to existing conversation
3. Continue conversation
4. Send image
5. Change settings
6. Test dark mode
7. Logout
8. Login again

**Journey 3: Poor Network Conditions**
1. Start conversation on WiFi
2. Switch to cellular (poor signal)
3. Send message
4. Enable airplane mode
5. Attempt to send message (queues)
6. Disable airplane mode
7. Message sends
8. Verify conversation continuity

**Test on:**
- iPhone SE (minimum spec)
- iPhone 15 Pro
- iPad (10.2")
- iPad Pro
- iOS 15, 16, 17, 18

#### 3.5.3 Regression Testing (2 days)
**Dependencies:** 3.5.2
**Assigned to:** QA
**Deliverables:** Regression test results

**Test all previously working features:**
- Verify no regressions from new changes
- Test all critical paths
- Test all device sizes
- Test all iOS versions

#### 3.5.4 Bug Triage & Prioritization (1 day)
**Dependencies:** 3.5.1, 3.5.2, 3.5.3
**Assigned to:** Product Manager + Tech Lead
**Deliverables:** Prioritized bug list

**Bug Priority:**
- **P0 (Critical):** Blocker for release (crashes, data loss, security)
- **P1 (High):** Major functionality broken
- **P2 (Medium):** Minor issue, has workaround
- **P3 (Low):** Nice to fix, not blocking

**Triage:**
- Assign severity
- Assign owner
- Estimate fix time
- Schedule for fix

#### 3.5.5 Bug Fixing - Critical (P0) (3 days)
**Dependencies:** 3.5.4
**Assigned to:** iOS Developer
**Deliverables:** All P0 bugs fixed

**Fix all critical bugs:**
- Crashes
- Data loss issues
- Security vulnerabilities
- Authentication failures
- Complete blockers

**Verify:**
- Bug fixed
- Test case added
- Regression test passed
- No new issues introduced

#### 3.5.6 Bug Fixing - High Priority (P1) (5 days)
**Dependencies:** 3.5.5
**Assigned to:** iOS Developer
**Deliverables:** All P1 bugs fixed

**Fix all high priority bugs:**
- Major functionality issues
- Performance problems
- Critical UX issues
- Accessibility problems

#### 3.5.7 Bug Fixing - Medium Priority (P2) (3 days)
**Dependencies:** 3.5.6
**Assigned to:** iOS Developer
**Deliverables:** P2 bugs fixed (as time permits)

**Fix P2 bugs:**
- Minor issues with workarounds
- Non-critical UX issues
- Edge cases

**Decision:** Some P2 bugs may be deferred to post-launch if time constrained

#### 3.5.8 Code Quality Review (2 days)
**Dependencies:** Bug fixes complete
**Assigned to:** Tech Lead
**Deliverables:** Code review complete

**Review:**
- Code style consistency (SwiftLint passing)
- Architecture adherence
- Test coverage (target: >70%)
- Documentation completeness
- TODO/FIXME items addressed
- Dead code removed
- Debug code removed

#### 3.5.9 Security Audit (2 days)
**Dependencies:** 3.5.8
**Assigned to:** Security Lead + Tech Lead
**Deliverables:** Security audit report

**Audit:**
1. **Data Security**
   - Keychain usage for sensitive data ✓
   - No hardcoded secrets ✓
   - Secure communication (TLS) ✓
   - Certificate pinning (if required) ✓

2. **Authentication**
   - OAuth implementation correct ✓
   - Token storage secure ✓
   - Token refresh secure ✓
   - Session timeout implemented ✓

3. **Code Security**
   - No SQL injection risks ✓
   - No XSS vulnerabilities ✓
   - Input validation ✓
   - Secure coding practices ✓

4. **Third-Party Dependencies**
   - All SDKs up to date ✓
   - Known vulnerabilities checked ✓
   - Licenses reviewed ✓

5. **App Security**
   - Jailbreak detection (if required) ✓
   - SSL pinning (if required) ✓
   - Screenshot prevention (sensitive screens) ✓

**Tools:**
- Dependency-check for vulnerable dependencies
- Static analysis tools
- Manual code review

#### 3.5.10 Sprint 9-10 Review (1 day)
**Dependencies:** All Sprint 9-10 tasks
**Assigned to:** Team
**Deliverables:** Sprint review, beta-ready build

**Review:**
- All critical bugs fixed
- Test results reviewed
- Security audit passed
- Code quality approved
- Beta build created

**Decision Gate:** Approve to proceed to Beta Testing (Phase 4)

---

## Phase 4: Beta Testing (Weeks 31-34)

**Goal:** User validation and production readiness

### Tasks

#### 4.1 Beta Preparation (2 days)
**Dependencies:** Phase 3 complete, beta-ready build
**Assigned to:** iOS Lead + Product Manager
**Deliverables:** Beta program ready

**Preparation:**
1. **TestFlight Setup**
   - Upload build to TestFlight
   - Configure beta information
   - Create test groups (internal, external)
   - Set up feedback collection

2. **Beta Tester Recruitment**
   - Recruit 20-50 beta testers
   - Mix of user types:
     - Power users (heavy app usage)
     - Connectivity-challenged users (main target)
     - Diverse devices (iPhone SE to iPhone Pro Max, iPads)
     - Diverse iOS versions (15-18)

3. **Beta Documentation**
   - Beta testing guide
   - Known issues list
   - How to provide feedback
   - FAQs

4. **Feedback Channels**
   - TestFlight built-in feedback
   - Email address for feedback
   - Survey form
   - Slack channel (if internal testers)

#### 4.2 Internal Beta (Week 1, Days 1-5)
**Dependencies:** 4.1
**Assigned to:** QA + Product Team
**Deliverables:** Internal beta feedback

**Internal Testing:**
- Team members use app daily
- Test all critical paths
- Test edge cases
- Document issues

**Daily Stand-ups:**
- Review bugs found
- Prioritize fixes
- Track progress

#### 4.3 External Beta Wave 1 (Week 2, Days 1-7)
**Dependencies:** 4.2, critical issues from internal beta fixed
**Assigned to:** Product Manager + Support
**Deliverables:** Beta wave 1 feedback

**Beta Testers:**
- 10-20 external users
- Diverse user profiles
- Actively engaged

**Monitoring:**
- Crash-free rate (target: >99%)
- Daily active users
- Session duration
- Bug reports
- User feedback

**Communication:**
- Welcome email
- Mid-week check-in
- End of week survey

#### 4.4 Bug Fixing - Beta Wave 1 (3 days)
**Dependencies:** 4.3
**Assigned to:** iOS Developer
**Deliverables:** Critical beta issues fixed

**Fix:**
- P0 bugs immediately
- P1 bugs as soon as possible
- Document P2/P3 bugs for post-launch

**New Build:**
- Upload fixed build to TestFlight
- Notify testers of update

#### 4.5 External Beta Wave 2 (Week 3, Days 1-7)
**Dependencies:** 4.4
**Assigned to:** Product Manager + Support
**Deliverables:** Beta wave 2 feedback

**Beta Testers:**
- Expand to 30-50 users
- Include all wave 1 testers
- Add new testers

**Focus:**
- Longer-term usage (full week)
- Real-world scenarios
- Production-like conditions

**Data Collection:**
- Analytics data review
- Crash reports
- Performance metrics
- User satisfaction survey

#### 4.6 Bug Fixing - Beta Wave 2 (3 days)
**Dependencies:** 4.5
**Assigned to:** iOS Developer
**Deliverables:** Beta issues fixed

**Fix:**
- Any remaining P0/P1 bugs
- Selected P2 bugs (if time)

**Final Beta Build:**
- Upload to TestFlight
- This becomes release candidate

#### 4.7 Production Readiness Review (1 day)
**Dependencies:** 4.6
**Assigned to:** Tech Lead + Product Manager
**Deliverables:** Production readiness checklist

**Checklist:**
- [ ] All P0 bugs fixed
- [ ] All P1 bugs fixed or acceptable workarounds documented
- [ ] Crash-free rate >99%
- [ ] Performance benchmarks met
- [ ] Security audit passed
- [ ] Accessibility testing passed
- [ ] Legal/compliance review passed (if required)
- [ ] App Store assets ready (screenshots, descriptions)
- [ ] Privacy policy updated
- [ ] Terms of service updated
- [ ] Support documentation ready
- [ ] User migration plan ready
- [ ] Rollback plan ready

#### 4.8 Beta Feedback Analysis (2 days)
**Dependencies:** 4.5
**Assigned to:** Product Manager + UX Designer
**Deliverables:** Beta feedback report

**Analyze:**
1. **User Satisfaction**
   - Overall satisfaction scores
   - Feature satisfaction
   - Would recommend? (NPS)

2. **Usability Issues**
   - Confusing UI elements
   - Navigation issues
   - Missing features
   - Desired improvements

3. **Performance**
   - Perceived speed
   - Battery impact
   - Connectivity improvement validated?

4. **Bugs Found**
   - Categorized by severity
   - Fix status
   - Remaining issues for v1.1

**Output:**
- Go/no-go recommendation
- Post-launch roadmap priorities

#### 4.9 Release Candidate Build (1 day)
**Dependencies:** 4.6, 4.7
**Assigned to:** iOS Lead
**Deliverables:** Release candidate ready for App Store

**Final Build:**
- Version number (e.g., 1.0.0)
- Build number (incrementing)
- All fixes applied
- Final testing passed
- Archived for App Store submission

#### 4.10 Phase 4 Review & Go/No-Go (1 day)
**Dependencies:** All Phase 4 tasks
**Assigned to:** Product Manager + Stakeholders
**Deliverables:** Approval to submit to App Store

**Review:**
- Beta testing results
- Metrics review
- Risk assessment
- User feedback summary

**Decision:** Approve App Store submission or address remaining issues

---

## Phase 5: App Store Submission & User Migration (Weeks 35-40)

**Goal:** Launch app and migrate users from mobile publisher

### Tasks

#### 5.1 App Store Assets Preparation (3 days)
**Dependencies:** Phase 4 approval
**Assigned to:** Product Manager + Designer
**Deliverables:** All App Store assets ready

**App Store Connect:**

1. **App Information**
   - App name
   - Subtitle (if desired)
   - Privacy policy URL
   - Support URL
   - Marketing URL (if applicable)

2. **Screenshots** (Required for all device sizes)
   - iPhone 6.7" (iPhone 15 Pro Max)
     - 5-10 screenshots
   - iPhone 6.5" (iPhone 15 Plus)
     - 5-10 screenshots
   - iPhone 5.5" (iPhone 8 Plus) - Optional
   - iPad Pro 12.9" (6th gen)
     - 5-10 screenshots
   - iPad Pro 12.9" (2nd gen) - Optional

   **Screenshots to capture:**
   - Login screen
   - Experience Cloud home
   - Native messaging interface
   - Conversation view
   - Settings screen
   - Key features highlighted

3. **App Preview Videos** (Optional but recommended)
   - 30-second video for iPhone
   - 30-second video for iPad
   - Demonstrate key features
   - Show connectivity resilience (if possible)

4. **App Icon**
   - 1024x1024 px (App Store icon)
   - No alpha channel
   - High quality

5. **Description**
   - Primary language description
   - Keywords for search optimization
   - What's new in this version
   - Promotional text

6. **Categories**
   - Primary category (e.g., Business, Productivity)
   - Secondary category (optional)

7. **Age Rating**
   - Complete questionnaire
   - Determine appropriate age rating

8. **Pricing & Availability**
   - Free (likely)
   - Availability territories
   - Pre-order (if applicable)

#### 5.2 App Store Review Preparation (2 days)
**Dependencies:** 5.1
**Assigned to:** iOS Lead + Product Manager
**Deliverables:** App Store review notes

**Review Notes:**
1. **Demo Account**
   - Provide Salesforce test credentials
   - Include instructions for reviewer

2. **Special Instructions**
   - How to test messaging (if agent needed)
   - Experience Cloud site access
   - Any non-obvious features

3. **Compliance**
   - Export compliance (encryption)
   - COPPA compliance (if applicable)
   - Data usage disclosure

**Pre-Submission Checklist:**
- [ ] App builds and runs on physical device
- [ ] All features functional
- [ ] No placeholder content
- [ ] No test/debug features
- [ ] Privacy policy accessible
- [ ] Crash-free
- [ ] Permissions requested with clear purpose strings
- [ ] Third-party login works (Salesforce)
- [ ] In-app purchases (if any) configured
- [ ] Subscription (if any) configured

#### 5.3 App Store Submission (1 day)
**Dependencies:** 5.2
**Assigned to:** iOS Lead
**Deliverables:** App submitted to App Store

**Submission Process:**
1. Upload release candidate to App Store Connect
2. Complete all metadata
3. Add build to version
4. Submit for review

**Typical Review Time:** 1-3 days (sometimes up to 7 days)

#### 5.4 Migration Plan Finalization (3 days)
**Dependencies:** Phase 4 approval
**Assigned to:** Product Manager
**Deliverables:** Detailed migration plan

**Migration Strategy:**

**Option 1: Phased Rollout**
- Week 1: 10% of users (connectivity-challenged users first)
- Week 2: 25% of users
- Week 3: 50% of users
- Week 4: 100% of users

**Option 2: User Choice**
- Make new app available
- Communicate benefits
- Users migrate voluntarily
- Sunset old app after X months

**Option 3: Force Migration**
- Announce sunset date for mobile publisher
- All users must migrate by deadline
- Higher risk but cleaner

**Migration Communications:**
1. **Pre-Launch (2 weeks before)**
   - Announce new app
   - Explain benefits
   - Provide timeline

2. **Launch Day**
   - App available in App Store
   - Installation instructions
   - FAQ published

3. **Post-Launch**
   - Weekly reminders for non-migrated users
   - Support for migration issues
   - Celebrate milestones (50% migrated, etc.)

#### 5.5 Support Documentation (3 days)
**Dependencies:** Phase 4 complete
**Assigned to:** Product Manager + Technical Writer
**Deliverables:** Complete support documentation

**Documentation:**
1. **User Guide**
   - How to download and install
   - How to login
   - How to navigate app
   - How to use messaging
   - How to change settings
   - FAQs

2. **Admin Guide**
   - How to configure Salesforce org
   - How to monitor adoption
   - How to troubleshoot issues

3. **Developer Guide** (if applicable)
   - How to customize
   - How to extend
   - API documentation

4. **Troubleshooting Guide**
   - Common issues and solutions
   - How to get support
   - How to report bugs

5. **Video Tutorials** (optional)
   - Getting started
   - Key features
   - Tips and tricks

#### 5.6 Support Team Training (2 days)
**Dependencies:** 5.5
**Assigned to:** Product Manager + Support Lead
**Deliverables:** Support team ready

**Training:**
1. **App Overview**
   - Features and capabilities
   - Differences from mobile publisher
   - Benefits

2. **Common Issues**
   - Login problems
   - Notification issues
   - Connectivity issues
   - Settings questions

3. **Escalation Process**
   - When to escalate
   - How to gather debug info
   - How to file bugs

4. **Tools**
   - TestFlight access (for diagnosing issues)
   - Analytics dashboard
   - Crash reporting dashboard

#### 5.7 App Store Review Process (Variable, 1-7 days)
**Dependencies:** 5.3
**Assigned to:** iOS Lead + Product Manager
**Deliverables:** App approved or issues addressed

**Possible Outcomes:**

**Approved ✓**
- App goes live
- Proceed to 5.8

**Rejected ✗**
- Review rejection reasons
- Address issues
- Resubmit
- Delay launch timeline

**Metadata Rejected**
- Fix metadata issues
- Resubmit
- Minimal delay

**Common Rejection Reasons:**
- Broken functionality
- Missing demo account
- Misleading screenshots
- Privacy policy issues
- Guideline violations

**If Rejected:**
1. Carefully review rejection message
2. Address all issues
3. Test thoroughly
4. Respond to reviewer (if applicable)
5. Resubmit

#### 5.8 Launch Preparation (1 day)
**Dependencies:** App Store approval
**Assigned to:** Product Manager + Marketing
**Deliverables:** Launch ready

**Preparation:**
1. **Communication Materials**
   - Email announcement
   - In-app message (in mobile publisher)
   - Website announcement
   - Social media posts (if applicable)

2. **App Store Optimization**
   - Monitor keyword rankings
   - Respond to reviews

3. **Support Readiness**
   - Support team on standby
   - Escalation process ready
   - FAQ published

4. **Monitoring Setup**
   - Analytics dashboard watching
   - Crash reporting monitoring
   - Performance metrics tracking

#### 5.9 App Launch (1 day)
**Dependencies:** 5.8
**Assigned to:** Product Manager
**Deliverables:** App live in App Store

**Launch Day:**
1. Set app to "Available" in App Store Connect
2. Send announcement to users
3. Post on website/social media
4. Monitor for issues
5. Respond to user feedback

**Monitoring:**
- First hour: Check every 15 minutes
- First day: Check hourly
- First week: Check daily
- Crash rate
- Download numbers
- User reviews
- Support tickets

#### 5.10 Post-Launch Week 1 (1 week)
**Dependencies:** 5.9
**Assigned to:** iOS Developer + Support
**Deliverables:** Week 1 issues addressed

**Activities:**
1. **Monitor Metrics**
   - Download rate
   - Crash-free rate (target: >99.5%)
   - Active users
   - User reviews (App Store)
   - Support tickets

2. **Issue Triage**
   - Categorize all reported issues
   - Fix critical issues immediately
   - Plan fixes for non-critical

3. **User Feedback**
   - Respond to App Store reviews
   - Acknowledge support tickets
   - Gather feedback for v1.1

4. **Hotfix (if needed)**
   - Critical bug found → immediate fix
   - Build 1.0.1
   - Emergency submission to App Store

#### 5.11 Migration Monitoring (Ongoing, 2-4 weeks)
**Dependencies:** 5.9
**Assigned to:** Product Manager
**Deliverables:** Migration progress reports

**Track:**
- Users migrated to new app
- Users still on mobile publisher
- Migration issues reported
- User satisfaction

**Communications:**
- Reminder emails for non-migrated users
- Support for migration questions
- Celebration of milestones

**Decision Points:**
- When to sunset mobile publisher?
- When to force migration?
- When to stop supporting old app?

#### 5.12 Post-Launch Retrospective (1 day)
**Dependencies:** 5.10
**Assigned to:** Full Team
**Deliverables:** Retrospective report

**Retrospective:**
1. **What Went Well**
   - Successes to celebrate
   - Processes that worked
   - Team collaboration wins

2. **What Didn't Go Well**
   - Challenges faced
   - Bugs that slipped through
   - Estimation accuracy

3. **What We Learned**
   - Technical learnings
   - Process improvements
   - Team growth

4. **Action Items for Next Project**
   - Process improvements
   - Tool changes
   - Training needs

#### 5.13 v1.1 Planning (1 week)
**Dependencies:** 5.10, 5.11
**Assigned to:** Product Manager + Tech Lead
**Deliverables:** v1.1 roadmap

**Prioritize for v1.1:**
- Bugs from v1.0 (P2/P3)
- User-requested features
- Performance improvements
- Deferred features from MVP

**Timeline for v1.1:**
- 2-3 months after v1.0
- Address top user feedback
- Continuous improvement

---

## Dependencies Summary

### Critical Path

```
Phase 0 (Feature Inventory)
  ↓
Phase 1 (POC) ← Must validate before proceeding
  ↓ Go/No-Go Decision
Phase 2 (Planning)
  ↓
Phase 3 (Development) ← Longest phase
  ├─ Sprint 0: Foundation
  ├─ Sprint 1-2: Push Notifications
  ├─ Sprint 3-4: Navigation & UI
  ├─ Sprint 5-6: Settings & UX
  ├─ Sprint 7-8: Analytics & Performance
  └─ Sprint 9-10: Testing & Bugs
  ↓
Phase 4 (Beta Testing)
  ↓ Go/No-Go Decision
Phase 5 (Launch & Migration)
```

### Key Decision Gates

1. **After Phase 0:** Approve feature scope and timeline
2. **After Phase 1:** Go/No-Go for full development
3. **After Phase 3:** Approve beta testing
4. **After Phase 4:** Approve App Store submission
5. **During Phase 5:** Monitor launch, decide migration timeline

### External Dependencies

- **Salesforce Org:** Configuration, credentials, APIs
- **Apple Developer Account:** Code signing, TestFlight, App Store
- **Experience Cloud Admin:** Hide web chat button, configure site
- **Backend Team:** JWT generation (if using User Verified auth instead of Passthrough)
- **Support Team:** Training, documentation, user assistance

---

## Risk Management

### High-Risk Items

| Risk | Impact | Mitigation |
|------|--------|------------|
| POC fails to solve connectivity | Project cancelled | Thorough POC testing, fallback options |
| App Store rejection | Launch delay | Early guideline review, demo account ready |
| Performance issues on iPad | Poor UX | Regular performance testing throughout development |
| User adoption low | Migration fails | Beta testing, communication plan, training |
| Timeline overrun | Budget impact | Buffer time, ruthless prioritization, MVP scope |
| Critical bug post-launch | User churn | Comprehensive testing, monitoring, hotfix process |

### Mitigation Strategies

1. **Buffer Time:** Add 10-15% buffer to each phase
2. **Regular Testing:** Don't wait until the end
3. **Incremental Delivery:** Show progress via sprints
4. **User Feedback:** Beta testing is critical
5. **Rollback Plan:** Can revert to mobile publisher if needed
6. **Phased Migration:** Don't force all users at once

---

## Success Metrics

### Development Success
- [ ] All MVP features delivered
- [ ] Crash-free rate >99%
- [ ] Performance benchmarks met
- [ ] App Store approval on first submission
- [ ] Launch on target date

### User Success
- [ ] Session retention >90% (vs current baseline)
- [ ] User satisfaction >4.5/5
- [ ] App Store rating >4.5
- [ ] Support tickets reduced by 50%
- [ ] 80%+ users migrated within 3 months

### Business Success
- [ ] Development completed within budget
- [ ] Connectivity issues resolved
- [ ] User productivity improved
- [ ] Positive ROI within 12 months

---

## Team & Roles

### Core Team

| Role | Responsibility | Time Allocation |
|------|---------------|-----------------|
| **iOS Developer (Lead)** | Architecture, core development, code review | 100% (40 hrs/week) |
| **iOS Developer (Mid/Junior)** | Feature development, bug fixes | 100% (40 hrs/week) |
| **QA Engineer** | Test planning, manual testing, automation | 100% (40 hrs/week) |
| **UX Designer** | UI/UX design, assets, user research | 50% (20 hrs/week) |
| **Product Manager** | Requirements, roadmap, stakeholder management | 25% (10 hrs/week) |
| **Project Manager** | Timeline, coordination, risk management | 25% (10 hrs/week) |

### Supporting Roles

| Role | When Needed | Time Allocation |
|------|-------------|-----------------|
| **iOS Architect** | Phase 2, architecture reviews | As needed |
| **Security Lead** | Phase 2, Phase 3 (Sprint 9-10) | As needed |
| **Backend Developer** | JWT implementation (if needed) | As needed |
| **Experience Cloud Admin** | Configuration, hide chat button | As needed |
| **DevOps Engineer** | CI/CD setup, infrastructure | Phase 2, as needed |
| **Technical Writer** | Documentation | Phase 5 |
| **Marketing** | Launch communications | Phase 5 |
| **Support Lead** | Training, support readiness | Phase 4-5 |

---

## Deliverables Checklist

### Phase 0
- [ ] Feature inventory spreadsheet
- [ ] User interview report
- [ ] MVP feature list
- [ ] Requirements document
- [ ] Timeline estimate
- [ ] Project charter (signed)

### Phase 1
- [ ] UIKitMIAW.swift configured
- [ ] Network resilience test results
- [ ] Comparison report (native vs web)
- [ ] User validation feedback
- [ ] POC validation report
- [ ] Go/No-Go decision

### Phase 2
- [ ] Technical architecture document
- [ ] Data model documentation
- [ ] UI/UX designs (all screens)
- [ ] Security requirements document
- [ ] Development environment setup
- [ ] Sprint plan
- [ ] Risk register

### Phase 3
- [ ] Production-ready iOS app (Universal)
- [ ] Unit tests
- [ ] Integration tests
- [ ] Performance test results
- [ ] Security audit report
- [ ] Code quality report
- [ ] Beta-ready build

### Phase 4
- [ ] Beta testing feedback report
- [ ] User satisfaction survey results
- [ ] Bug fix documentation
- [ ] Production readiness checklist (completed)
- [ ] Release candidate build

### Phase 5
- [ ] App Store assets
- [ ] Support documentation
- [ ] Migration plan
- [ ] App Store approval
- [ ] Launch communications
- [ ] Post-launch reports
- [ ] Retrospective report
- [ ] v1.1 roadmap

---

## Timeline Gantt Chart (High-Level)

```
Week  1-2:  [Phase 0: Feature Inventory          ]
Week  3-6:  [Phase 1: POC Validation                          ]
Week  7-8:  [Phase 2: Planning & Design      ]
Week  9-10: [Sprint 0: Foundation           ]
Week 11-14: [Sprint 1-2: Push Notifications                   ]
Week 15-18: [Sprint 3-4: Navigation & UI                      ]
Week 19-22: [Sprint 5-6: Settings & UX                        ]
Week 23-26: [Sprint 7-8: Analytics & Performance              ]
Week 27-30: [Sprint 9-10: Testing & Bug Fixes                 ]
Week 31-34: [Phase 4: Beta Testing                            ]
Week 35-36: [Phase 5: App Store Submission  ]
Week 37-38: [Phase 5: Review Process        ]
Week 39-40: [Phase 5: Launch & Migration                      ]
```

**Total: 40 weeks (~10 months)**

---

## Budget Considerations

### Team Costs (Approximate)

Assuming US market rates:
- iOS Developer (Lead): $150-200/hr × 40 hrs/week × 40 weeks = $240k-320k
- iOS Developer (Mid): $100-150/hr × 40 hrs/week × 40 weeks = $160k-240k
- QA Engineer: $80-120/hr × 40 hrs/week × 40 weeks = $128k-192k
- UX Designer: $100-150/hr × 20 hrs/week × 40 weeks = $80k-120k
- Product Manager: $100-150/hr × 10 hrs/week × 40 weeks = $40k-60k
- Project Manager: $100-150/hr × 10 hrs/week × 40 weeks = $40k-60k

**Total Team Cost: $688k-992k** (midpoint: ~$840k)

### Infrastructure & Tools

- Apple Developer Account: $99/year
- CI/CD (Bitrise/CircleCI): $200-500/month × 10 months = $2k-5k
- Analytics (Mixpanel/Amplitude): $0-1k/month × 10 months = $0-10k
- Crash Reporting (Crashlytics): Free
- Project Management (Jira): $10-20/user/month × 6 users × 10 months = $600-1,200
- Design Tools (Figma): $15/user/month × 2 users × 10 months = $300
- TestFlight: Free
- Misc tools: $2k-5k

**Total Infrastructure: $5k-22k**

### Total Budget Estimate

**$700k - $1M** (10 months)

For smaller budget:
- Contract developers (often lower rates)
- Smaller team (1 iOS dev instead of 2)
- Offshore team (significantly lower rates)
- Reduce scope (fewer features in MVP)

**Minimum viable budget: $250k-400k** (with offshore/contract team, reduced scope)

---

## Next Steps

1. **Review this plan** with stakeholders
2. **Adjust scope and timeline** based on budget and priorities
3. **Secure team resources** (hire or allocate)
4. **Begin Phase 0** (Feature Inventory) immediately
5. **Schedule regular check-ins** (weekly during development)
6. **Establish communication channels** (Slack, Jira, etc.)
7. **Kick off project** with team alignment meeting

---

**Document Version:** 1.0
**Last Updated:** January 21, 2026
**Owner:** Product Manager
**Review Cadence:** Weekly during active phases
