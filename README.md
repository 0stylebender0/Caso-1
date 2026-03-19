# DUA Streamliner

---

## Problem

The preparation of the Single Administrative Document (DUA) is a manual, repetitive, and error-prone process that requires interpreting multiple source documents in diferent formats such as Excel, Word, PDF, and scanned images. This work heavily depends on the expert knowledge of customs agents, creating bottlenecks, high operational costs, and risk of rejections or fines from customs authorities. This project aims to automate this process through artificial intelligence, automatically extracting and mapping relevant information to the official template of the Ministry of Finance of Costa Rica.

This project aims to automate this process through artificial intelligence, automatically extracting and mapping relevant information to the official template. The goal is not to replace the customs expert but to transform them into a strategic validator, eliminating manual transcription work and allowing them to focus on high-value tasks such as tariff classification optimization, regulatory compliance analysis, and exception handling. By reducing processing time from hours to minutes and minimizing human error, the system seeks to make Costa Rican foreign trade more efficient, competitive, and reliable.

---

## Authors

- Daniel Campos     2023117952
- Kevin Espinoza    2023055841

---

# FrontEnd design

## 1.1 Technology stack:
- Application type:                             Single Page Application (SPA)
- Web framework:                                Angular 19.2 - Node.js 22 LTS
- Web server:                                   Express.js 5.1
- Coding Language:                              TypeScript 5.7
- Unit testing framework:                       Jasmine 5.6 + Karma 6.4
- Data validation framework:                    Joi 17.13
- Code prettier framework:                      Prettier 3.5
- Code style framework:                         ESLint 9.22 + Angular ESLint 19.2
- Integration testing tools:                    Cypress 14.3
- Cloud service:                                Amazon Web Services (AWS)
- Hosted services within the cloud service:     AWS Lambda, AWS API Gateway, AWS Textract, AWS Bedrock
- Code repositories service:                    GitLab
- Code automation task tool:                    Husky 10.1
- CI/CD pipelines technology:                   GitLab CI/CD 17.5
- Environments:                                 Development, staging, production
- Environment deployments tools:                GitLab Environments
- Observability framework:                      AWS CloudWatch + X-Ray

## 1.2 UX UI analysis:

### Core business process

#### Login

The user enters their email and password. The system validates the credentials against the user directory. If the credentials are correct, the system creates a session and redirects the user to the home screen. If the credentials are incorrect, the system displays an error message and allows the user to try again. If the user has forgotten their password, they can request a reset link to their registered email.

#### Select DUA Template

The user arrives at the new declaration screen. The system presents the available official templates published by Ministerio de Hacienda de Costa Rica: Import, Export, Transit, and Warehouse. The user selects the template that corresponds to the operation they need to declare. Once selected, the system highlights the chosen template and enables the next step.

The user then indicates the location of the source documents by dragging a folder into the designated area or typing a local or network path. The system scans the folder and identifies all readable files. It classifies each file by type: PDF, Excel, Word, or image. The system displays a summary showing how many files of each type were found. If no valid files are detected, the system notifies the user and asks them to verify the folder path. Once the user confirms the file summary is correct, they trigger the processing.

#### Process Monitoring

The system begins processing the documents and the user is taken to the monitoring screen. The screen shows the current job identifier so the user can reference this declaration later.

The process runs through six sequential stages:

**Stage 1 — File reading.** The system opens each file in the folder. For Excel and Word files, it reads the structured content directly. For PDFs, it extracts text layer by layer. For images, it queues them for OCR processing. If a file cannot be opened or is corrupted, the system logs the error and continues with the remaining files without stopping the entire job.

**Stage 2 — OCR and image interpretation.** The system runs optical character recognition on each image and scanned PDF page. It attempts to reconstruct the text structure, identifying lines, columns, and blocks of information. If the image quality is too low for reliable recognition, the system flags the file with a reduced-confidence warning and continues.

**Stage 3 — Semantic data extraction.** The system passes the extracted text through an AI model trained on customs terminology. The model identifies and extracts specific entities regardless of the document format or layout: importer and exporter names, tax identifiers, supplier information, commercial and tariff descriptions of goods, quantities, weights, FOB and CIF values, Incoterms, transport details, invoice numbers and dates, country of origin, and the applicable customs regime. Each extracted value is assigned a confidence score based on how clearly and consistently it appeared across the source documents.

**Stage 4 — Mapping to the official DUA template.** The system maps each extracted value to its corresponding field in the official DUA template selected by the user in the previous screen. Fields that appear in multiple source documents are cross-referenced and the most consistent value is selected. Fields where the extracted data is ambiguous or contradictory are flagged for review.

**Stage 5 — Consistency validation.** The system performs logical checks across the mapped fields: it verifies that value totals are internally consistent, that currencies are uniform, that dates are chronologically valid, that the declared weight matches across documents, and that the country of origin is coherent with the supplier information. Any inconsistency is logged and the affected fields are marked accordingly.

**Stage 6 — Word document generation.** The system produces a .docx file using the official DUA template. Each field is filled with the extracted and validated value. Fields are color-coded based on their confidence level. The document is stored and made available for download.

Throughout the entire process, the user sees a progress bar and a live log that shows each file as it is processed, including any warnings or errors. The user does not need to take any action during this stage. When all six stages are complete, the system notifies the user that the result is ready and redirects them to the result screen.

#### Review and Download Result

The user arrives at the result screen and sees the pre-filled DUA organized into sections that mirror the official template structure.

Each field displays its extracted value and is visually coded by confidence level. Fields marked in green were extracted with high confidence and are consistent across source documents. Fields marked in yellow were extracted with medium confidence, meaning the value was found but in an ambiguous context or with minor inconsistencies across documents. Fields marked in red could not be reliably extracted or failed a consistency check, and require the user to provide or correct the value manually.

The user reviews each section in order: declarant data, goods description, values and transport, and supporting documents. If a field requires correction, the user clicks on it and enters the correct value directly. The system saves the edit and updates the confidence indicator for that field to reflect that it was manually validated.

Once the user has reviewed all fields and corrected the ones marked in red, they download the final .docx file. The downloaded document contains the completed DUA ready to be reviewed by the customs agent before submission to the authority.

#### Logout

The user ends the session. The system invalidates the active session token, clears any temporary data from the browser, and redirects the user to the login screen.

#### Wireframes
- Login screen
![Login](/media/login.png)

- Select DUA template and source folder
![DUA Template and folder selection](/media/template-and-folder.png)

- Monitoring progress
![Monitoring](/media/monitoring.png)

- Final result
![Result](/media/final-result.png)


### UX test results

**Used Tool:** Maze and figma
**Date:** March 2025  
**Participants:** 3  
**Method:** Unmoderated remote test  

#### Login
![Metrics](/media/login-metrics.png)
![Heatmap](/media/heatmap-login.jpg)

#### Template selection, file upload and start process
![Metrics](/media/template-metrics.png)
![Heatmap](/media/heatmap-template.jpg)

#### Progress following
![Metrics](/media/progress-metrics.png)
![Heatmap](/media/heatmap-progress.jpg)

#### Results
![Heatmap](/media/heatmap-result.jpg)

#### Qualitative Feedback
![Opinion scale](/media/opinion-scale.png)

## 1.3 Component design strategy

Framework: 		Angular
Design strategy: 	Feature-based modular architecture

---

The frontend follows a feature-based modular architecture, where each core business process maps to an isolated Angular module with its own components, services, and routing:

- **AuthModule** → Login, session management
- **UploadModule** → Folder selection, file validation summary
- **MonitorModule** → Real-time progress tracking per processing stage
- **ReviewModule** → DUA field viewer, confidence indicators, inline editing
- **SharedModule** → Reusable UI elements (buttons, modals, status badges, confidence color coding)

---

- Reusability is achieved through a SharedModule containing presentational components (buttons, modals, status badges, confidence indicators) that receive data via @Input() and emit events via @Output(), decoupling UI from business logic.

- Styles and branding are centralized using SCSS design tokens (colors, typography, spacing) defined globally and consumed across all components, ensuring visual consistency — including the confidence color scheme (green/yellow/red). 

- Responsiveness is handled via Angular CDK's BreakpointObserver combined with CSS Flexbox/Grid, adapting layouts for desktop and tablet, the primary target devices for customs agents.

- Internationalization is managed through Angular i18n, with translation files structured per locale (Spanish as default, English as secondary), allowing the UI to scale to other markets without structural changes.

---

## 1.4 Security:

The DUA Streamliner applies a defense-in-depth security model, combining identity management, access control, secrets management, and transport-layer protections. Every layer is aligned with the Angular 19.2 / AWS / Azure Entra ID stack already defined in this document.

---

Authentication

Authentication is fully delegated to Azure Entra ID, which acts as the centralized identity provider for all users of the platform. The Angular SPA uses the MSAL (Microsoft Authentication Library) for Angular to initiate and handle the OAuth 2.0 / OpenID Connect flows.

- Protocol: OAuth 2.0 Authorization Code Flow with PKCE (Proof Key for Code Exchange).
- Token types issued: ID Token (user identity), Access Token (API calls), Refresh Token (session renewal).
- All tokens are stored in memory only — never in `localStorage` or `sessionStorage` — to prevent XSS-based token theft.
- Token expiry and renewal are managed transparently by MSAL, with silent token refresh via hidden iframes before expiry.
- The server name registered in Azure Entra ID for this application is:

```
customsidentityserver
```

---

Multi-Factor Authentication (MFA)

MFA is enforced for all user accounts through Conditional Access Policies configured in Azure Entra ID. The only accepted second factor is a mobile authenticator application (e.g., Microsoft Authenticator), generating time-based one-time passwords (TOTP).

- SMS-based OTP and email OTP are explicitly disabled to prevent SIM-swap and phishing attacks.
- Hardware tokens are not supported in the current scope.
- MFA step-up is automatically triggered by Conditional Access on every new session, regardless of location or device, to comply with customs authority data protection requirements.

---

Single Sign-On (SSO)

SSO is enabled through **Azure Entra ID**, allowing users to authenticate once and access all integrated services (Angular SPA, AWS API Gateway, reporting dashboards) without re-entering credentials. The SSO session lifetime and idle timeout are managed by Azure Entra ID Conditional Access policies and are not controlled at the application level.

---

Authorization — Roles and Permissions

The system defines two application roles, assigned per user in Azure Entra ID App Registrations. The Angular frontend reads the `roles` claim from the decoded ID Token to conditionally render UI elements, while the backend Lambda functions validate the Access Token and enforce the same rules server-side.

Manager

Users with the Manager role are responsible for platform administration, user governance, and template management. They do not perform customs declarations.

| Permission Code | Description |
|---|---|
| `MANAGE_USERS` | Create, update, deactivate, and assign roles to user accounts. |
| `VIEW_REPORTS` | Access operational and performance reports including processing times, error rates, and agent activity. |
| `EDIT_TEMPLATES` | Add, modify, or retire DUA templates published by the Ministerio de Hacienda de Costa Rica. |

Customs Agent

Users with the **Customs Agent** role are the primary operators of the system. They upload source documents, trigger AI processing, review results, and download the generated DUA. They have no access to administrative or reporting functions.

| Permission Code | Description |
|---|---|
| `LOAD_FILES` | Select and upload a folder containing source documents (PDF, Excel, Word, images) for a declaration. |
| `GENERATE_DUA` | Initiate the AI processing pipeline that extracts, maps, and validates data into the DUA template. |
| `DOWNLOAD_DUA` | Download the generated and reviewed `.docx` DUA file. |

---

Secrets Management — Azure Key Vault

All sensitive configuration is stored in **Azure Key Vault** and is never hardcoded in source code, CI/CD pipeline variables, or environment files committed to GitLab. The following categories of secrets are managed through the vault:

- Environment variables per environment (development, staging, production).
- API keys for AWS services (Textract, Bedrock, Lambda invocation).
- Azure Entra ID application client secrets and certificate thumbprints.
- Any third-party integration credentials.

At runtime, backend Lambda functions retrieve secrets using the AWS Secrets Manager / Azure Key Vault integration via the AWS Parameter Store bridge. The Angular frontend never accesses the vault directly; all secrets it requires are injected as environment-specific configuration by the CI/CD pipeline at build time, limited strictly to non-sensitive values (e.g., the Entra ID tenant ID and client ID, which are public by design in OAuth 2.0 flows).

---

Transport Security

- All communication between the Angular SPA and AWS API Gateway is enforced over TLS 1.2+ with HSTS headers.
- AWS API Gateway is configured to reject requests with TLS versions below 1.2.
- CORS is configured at the API Gateway level, whitelisting only the production, staging, and development origins.
- AWS CloudFront is used as the CDN for the Angular SPA, providing DDoS mitigation and edge-level TLS termination.

---

Session Management

Sessions are managed by Azure Entra ID in combination with the MSAL Angular library. The following controls apply:

- On logout, MSAL clears all in-memory tokens and triggers an Entra ID end-session request to invalidate the server-side session.
- Browser storage (`localStorage`, `sessionStorage`) is never used to persist tokens or session state.
- Idle session timeout is enforced via Conditional Access Policy in Entra ID, set to **60 minutes** of inactivity, after which the user must re-authenticate.
- Concurrent sessions from multiple devices are permitted but each maintains an independent token lifecycle.

---

Security Observability and Audit

Security-relevant events are logged and monitored using the observability stack defined in section 1.1 (AWS CloudWatch + X-Ray). The following event categories are captured:

- Authentication events: successful logins, failed login attempts, MFA challenges, and token refresh cycles — sourced from Azure Entra ID sign-in logs forwarded to CloudWatch via Azure Monitor Diagnostic Settings.
- Authorization failures: any API Gateway request rejected due to an invalid or insufficient Access Token.
- Secrets access: all Key Vault read operations are logged with actor identity and timestamp.
- File processing events: each document processed by AWS Textract and Bedrock is logged with the job identifier, user identity, and processing outcome.

Alerts are configured in CloudWatch Alarms to notify the Manager role when anomalous patterns are detected, such as repeated authentication failures or unusual API call volumes.

## 1.5 Layered Design

The frontend of DUA Streamliner is structured as a set of well-defined, vertically stacked layers where each layer has a single responsibility and communicates only with the layers adjacent to it. This separation prevents business logic from leaking into the UI, keeps external dependencies isolated, and makes each concern independently testable. The following describes each layer in detail, using the technology stack defined in section 1.1.

---

### SSR Layer — Express.js + Angular Universal

When a user navigates to the application, the request is received by an Express.js 5.1 server running on Node.js 22 LTS. Express handles Server-Side Rendering (SSR) by invoking Angular Universal to render the initial HTML on the server before sending it to the browser. This means the browser receives a fully-formed HTML document on the first load rather than an empty shell, which improves perceived performance and allows the Authentication Layer to be evaluated before any client-side JavaScript runs.

Express is also responsible for injecting environment-specific configuration into the rendered page at startup time. This configuration — such as the Azure Entra ID tenant ID and client ID — is pulled from the Settings Layer before the server begins serving requests.

---

### Authentication Layer — Azure Entra ID + MSAL Angular

Immediately after the SSR request is handled, the Authentication Layer is evaluated. This layer is implemented using the MSAL (Microsoft Authentication Library) for Angular, which integrates with Azure Entra ID as the identity provider via the OAuth 2.0 Authorization Code Flow with PKCE.

Angular route guards implemented with `CanActivateFn` intercept every navigation event. If the user does not have a valid in-memory Access Token, the guard redirects them to the Azure Entra ID login page. Upon successful authentication, Entra ID returns an ID Token, an Access Token, and a Refresh Token. MSAL stores all tokens exclusively in memory — never in `localStorage` or `sessionStorage` — and handles silent token renewal automatically before expiry.

The roles claim embedded in the decoded ID Token is read by this layer and made available to the Components Layer so that UI elements can be conditionally rendered based on whether the user holds the `Manager` or `Customs Agent` role. No routing is permitted until this layer confirms a valid, active session.

---

### Components Layer — Atomic Design

Once authentication is confirmed, Angular renders the component tree that corresponds to the current route. Components are organized following Atomic Design methodology, structured into five levels of increasing complexity:

**Atoms** are the smallest indivisible UI elements: input fields, buttons, confidence badges (green, yellow, red), progress bar segments, file type icons, and label chips. Each atom is a standalone Angular component with no dependencies on services or state.

**Molecules** are combinations of atoms that together form a functional UI unit: a form field composed of a label atom, an input atom, and a validation message atom; a file summary card composed of a file type icon atom and a count label atom; or a processing stage row composed of a status dot atom, a stage label atom, and a status tag atom.

**Organisms** are larger, self-contained sections of the interface: the credential form organism on the login screen, the template selection grid organism, the folder drop zone organism, the processing stage list organism with its live log, and the DUA field section organisms on the result screen (Section A, Section B, Section C). Organisms are aware of application state and interact with the Hooks Layer.

**Templates** define the layout structure of a screen without containing real data. They arrange organisms into a spatial grid, establish the header and navigation bar, and define how content regions are distributed. Templates are reusable across pages that share the same structural layout.

**Pages** are Angular routed components that bind a template to real data. Each page corresponds to one screen in the application: LoginPage, TemplateSelectorPage, ProcessMonitoringPage, and ResultPage. Pages are the entry point for route guards and are responsible for initiating the data flow that populates their template and organisms.

---

### Hooks Layer — Angular Services injected into Components

Within the Components Layer, a Hooks Layer provides the bridge between visual components and the application's business logic. In Angular, this pattern is implemented using injectable services scoped to the component or module level, combined with RxJS observables that components subscribe to via the `async` pipe.

Each organism or page component receives its dependencies through Angular's dependency injection system rather than instantiating them directly. This means a component never knows how data is fetched or how an operation is executed — it only knows what to render and what action to trigger. For example, the ProcessMonitoringPage component subscribes to a `jobStatus$` observable exposed by the ProcessingHookService. When the observable emits a new stage completion event, the component re-renders the affected stage row reactively without any imperative DOM manipulation.

This layer is also responsible for translating user gestures (a button click, a folder drop, a field edit) into calls on the Services Layer, and for propagating the result back to the component as observable state updates.

---

### Services Layer — Application Business Logic

The Services Layer contains all application-level operations. Services are singleton Angular injectables decorated with `providedIn: 'root'` or scoped to a specific module. They hold no UI logic and no direct DOM references. Their sole responsibility is to orchestrate the steps required to fulfill a business operation.

**AuthService** manages the session lifecycle: initiating login, handling the OAuth callback, reading the roles claim from the token, triggering logout, and exposing the current user identity as an observable.

**DeclarationService** handles the creation and lifecycle of a DUA declaration job: validating the selected template, submitting the source folder path to the backend, and exposing the job identifier for monitoring.

**FileIngestionService** is responsible for scanning the selected folder, classifying files by type, and constructing the payload that will be sent to the backend processing pipeline.

**ProcessingStatusService** polls the backend job status endpoint at a defined interval using RxJS `interval` and `switchMap`, emitting each new status update as an observable event. When the job reaches a terminal state (completed or failed), the polling subscription is automatically unsubscribed.

**ResultService** fetches the pre-filled DUA fields from the backend once processing is complete, exposes them as typed observables keyed by section, and handles field-level edit submissions back to the backend.

**DocumentService** handles the download of the generated `.docx` file by constructing a presigned AWS S3 URL request through the ApiClients Layer and triggering the browser download.

All services delegate external communication exclusively to the ApiClients Layer and never construct HTTP requests directly.

---

### Utils Layer — Shared Pure Functions

The Utils Layer contains stateless, pure TypeScript functions that are used across multiple layers without introducing dependencies. This layer includes date formatting and parsing utilities, currency and numeric formatters, confidence score interpreters (mapping a numeric score to the green/yellow/red classification), file type classifiers based on MIME type or extension, string sanitizers, and general-purpose TypeScript type guards used throughout the codebase.

Utils functions are imported directly where needed and are fully covered by unit tests written in Jasmine 5.6 running under Karma 6.4, since their pure nature makes them trivial to test in isolation.

---

### ApiClients Layer — External API Abstraction

The ApiClients Layer contains one class per external system that the frontend communicates with. Each class encapsulates the full communication contract with its corresponding API: the base URL, the required headers, the request construction, and the response parsing. No other layer constructs HTTP requests or parses raw HTTP responses.

**BackendApiClient** communicates with AWS API Gateway, which fronts the Lambda functions that orchestrate document processing. It sends the folder metadata and template selection, polls for job status, retrieves the extracted DUA fields, and submits manual field corrections. All requests include the Bearer Access Token retrieved from the AuthService.

**EntraIdApiClient** handles any direct calls to the Microsoft Graph API needed to resolve user profile information beyond what is available in the ID Token, such as display names and group memberships.

Every method in an ApiClients class returns an RxJS `Observable`. All API responses are immediately passed through the DataValidation Layer before being returned to the calling service. If validation fails, the Observable emits an error that is caught by the ExceptionHandling Layer.

---

### Settings Layer — Environment Configuration from Azure Key Vault

The Settings Layer is a singleton Angular service that provides typed access to all environment-specific configuration. During the Express.js SSR startup sequence, the server retrieves non-sensitive configuration values from Azure Key Vault using the Key Vault SDK, and injects them into the Angular application as environment tokens. The Angular Settings service reads these tokens at bootstrap time and exposes them as typed, read-only properties.

Configuration managed through this layer includes the AWS API Gateway base URL per environment (development, staging, production), the Azure Entra ID tenant ID and client ID, the AWS region, the S3 bucket name for document storage, and feature flags that control which DUA template types are enabled in a given environment.

The Angular frontend never accesses Azure Key Vault directly at runtime. The Settings Layer is populated once at server startup and the values flow into the client as part of the SSR-rendered page. This ensures that secrets are never exposed to the browser and that environment-specific behavior is fully controlled at the infrastructure level.

ApiClients read all base URLs and configuration values exclusively through the Settings Layer, so that changing an endpoint across environments requires only a Key Vault secret update, with no code change.

---

### Models Layer — Shared Typed Data Structures

The Models Layer defines all TypeScript interfaces, enums, and types that describe the data structures flowing through the application. This layer has no logic and no dependencies — it is a pure type definition library imported by every other layer.

Key models include `DeclarationJob` (job identifier, status, timestamps, associated template), `DuaField` (field code, extracted value, confidence score, manual override flag), `DuaSection` (section identifier, ordered list of DuaFields), `FileIngestionSummary` (counts by file type, list of file metadata), `ProcessingStage` (stage name, status enum, start and end timestamps), `UserIdentity` (email, display name, assigned role), and `ApiResponse<T>` (a generic wrapper for all API responses including error envelopes).

All layers import from Models, which means data structures are defined once and never duplicated. This is the only layer that every other layer is permitted to depend on freely, alongside Utils and State Management.

---

### DataValidation Layer — Joi Schema Validation

The DataValidation Layer is responsible for validating all data received from external sources before it is used anywhere in the application. It is implemented using Joi 17.13 and operates exclusively at the boundary between the ApiClients Layer and the rest of the application.

Each API response shape has a corresponding Joi schema. When an ApiClient receives a response, it passes the raw payload through the appropriate schema before converting it into a typed Model instance. If the payload does not conform to the schema — a missing required field, an unexpected type, a value outside a permitted range — validation throws an error that is caught by the ExceptionHandling Layer. This prevents malformed or unexpected data from propagating into the Services or Components layers, where it would cause unpredictable runtime behavior.

Validation schemas are colocated with the ApiClient classes they serve and are exported so they can be reused in unit tests to verify that mock responses conform to the expected contract.

---

### State Management Layer — Angular Signals + RxJS

The State Management Layer holds the client-side application state that must be shared across multiple components or persisted across navigation events within a session. In Angular 19.2, this is implemented using Angular Signals for synchronous, fine-grained reactive state, complemented by RxJS BehaviorSubjects for state that originates from asynchronous streams.

The state managed at this layer includes the authenticated user identity and role, the currently active declaration job and its status, the list of DUA sections and fields for the active result, and any field-level edits made by the user before downloading. Components and services read from this layer via signal reads or observable subscriptions and write to it only through well-defined update functions, preventing uncontrolled mutation.

This layer is accessible from all other layers, which allows, for example, the ProcessingStatusService to write a new stage status update that is immediately reflected in the ProcessMonitoringPage component without any explicit event passing between them.

---

### Notification Service Layer — Asynchronous Event Handling via AWS API Gateway Callbacks

Because document processing is a long-running asynchronous operation — potentially spanning several minutes as AWS Textract and AWS Bedrock work through the source documents — the frontend cannot rely on simple synchronous request-response cycles for status updates. The Notification Service Layer handles this by implementing a callback-based subscription model.

When the DeclarationService submits a new processing job to the backend, it registers a callback endpoint provided by AWS API Gateway's WebSocket API. The backend Lambda functions publish stage completion events to this WebSocket connection as each processing stage finishes. The Notification Service Layer in the frontend maintains the WebSocket connection, receives these events, and dispatches them to the appropriate RxJS subjects in the State Management Layer.

Other layers subscribe to the Notification Service by providing a handler function that is invoked when a specific event type is received. This decouples the event producer (the backend Lambda pipeline) from the event consumers (the ProcessingStatusService, the ResultService) without requiring any polling. When the WebSocket connection is lost due to a network interruption, the Notification Service Layer automatically attempts reconnection with exponential backoff and notifies the user through the ExceptionHandling Layer if the connection cannot be restored within a defined timeout.

---

### Exception Handling Layer — Shared Error Management

The ExceptionHandling Layer is a shared cross-cutting layer accessible from all other layers. It is implemented as a combination of an Angular `ErrorHandler` override (for uncaught runtime exceptions) and an RxJS `catchError` operator factory (for observable pipeline errors).

When any layer encounters an error — a failed API call, a Joi validation rejection, a WebSocket disconnection, an unexpected null value — it pipes the error through the ExceptionHandling Layer. This layer classifies the error by type: network errors, authentication errors (401/403, triggering a re-authentication flow), validation errors (logged as warnings with the offending payload), and unexpected application errors (logged as critical events and surfaced to the user as a generic error notification).

Error classification determines the recovery strategy: silent retry, redirect to login, display of a user-facing error message, or escalation to the Logs Layer. This centralization ensures that error handling behavior is consistent across the entire application and that no error is silently swallowed.

---

### Logs Layer — AWS CloudWatch via ApiClients

The Logs Layer provides structured logging classes used by every other layer to register system events. It exposes three severity levels — `info`, `warn`, and `error` — each accepting a structured payload that includes the event category, the affected layer, a correlation ID tied to the current declaration job, the authenticated user identity, and a timestamp.

Log entries are batched and sent asynchronously to AWS CloudWatch through the BackendApiClient, which forwards them to a dedicated logging Lambda function that writes to CloudWatch Logs. AWS X-Ray traces are automatically attached to outbound API calls made through the ApiClients Layer, providing distributed tracing across the frontend SSR server, AWS API Gateway, and the backend Lambda functions.

The Logs Layer never blocks the calling layer. Log dispatch is fire-and-forget, implemented as a non-awaited observable that the layer subscribes to internally. If a log write fails, the failure is recorded locally in the browser console but does not propagate as an error to the calling layer, preventing logging failures from affecting application functionality.

---

### Layer Interaction Diagram

```
       Browser
          |
          v
  Express.js SSR (Node.js 22 LTS)
          |
          v
  Authentication Layer (MSAL + Azure Entra ID)
          |
          v
  Components Layer (Angular 19.2 — Atomic Design)
  Atoms → Molecules → Organisms → Templates → Pages
          |
          v
       Hooks Layer (Angular DI + RxJS)
          |
          v
     Services Layer
          |
    +-----+------+----------+
    |            |           |
  Utils      ApiClients   Settings
                |               \
                |           Azure Key Vault
                v
       DataValidation (Joi 17.13)
                |
                v
        External APIs
        (AWS API Gateway → Lambda → Textract / Bedrock)
                |
                v
    Notification Service (AWS WebSocket API)

Shared across all layers:
  - Models (TypeScript interfaces)
  - State Management (Angular Signals + RxJS)
  - Exception Handling (Angular ErrorHandler + catchError)
  - Logs (AWS CloudWatch + X-Ray via ApiClients)

CI/CD:
  GitLab Repository
       |
  GitLab CI/CD 17.5 Pipelines
       |
  +----+----+----------+
  Dev    Staging    Production
       |
  AWS Lambda + AWS CloudFront + AWS API Gateway
```

## 1.6 Design patterns:

**Builder + Strategy — Document Processors**

Each source document type (`.docx`, `.xlsx`, `.pdf`, `.jpg`, `.png`) requires a different reading and parsing strategy. The Strategy Pattern defines a common `DocumentProcessor` interface with a `process(file)` method, and each concrete processor (WordProcessor, ExcelProcessor, PdfProcessor, ImageProcessor) implements it independently. The Builder Pattern is used to assemble the correct processor chain for a given job based on the file types found in the source folder, without the calling layer needing to know which processors are involved.

---

**Observer — NotificationService**

The NotificationService implements the Observer Pattern to distribute asynchronous processing stage events received from the AWS WebSocket API. Services such as ProcessingStatusService and ResultService register as subscribers and are notified when a new stage completion event is emitted. This decouples the WebSocket event source from the consumers, allowing new subscribers to be added without modifying the notification infrastructure.

---

**Adapter — Output Format Writers**

When generating the pre-filled DUA `.docx` file, different field types require different document constructs. The Adapter Pattern is applied through a `FormatAdapter` interface that normalizes the output of the AI extraction pipeline into a common write contract. Concrete adapters — `ParagraphAdapter`, `BulletsAdapter`, `TableAdapter`, `LabelAdapter`, and `AmountAdapter` — each translate a DUA field value into the appropriate Word document structure, isolating the document generation logic from the data extraction logic.

---

**Singleton — Shared Infrastructure Classes**

The following classes are instantiated once and shared across all layers for the lifetime of the application session:

- `ExceptionHandlingService` — centralized error classification and recovery
- `DocumentParsers` — one parser instance per file type, reused across jobs
- `Utils` — stateless pure function library, single shared reference
- `StateManagement` — single source of truth for application state via Angular Signals
- `ApiClients` — one client instance per external system (BackendApiClient, EntraIdApiClient)
- `SettingsService` — environment configuration loaded once at SSR startup from Azure Key Vault

In Angular 19.2, singleton scope is enforced by declaring these classes with `providedIn: 'root'`, ensuring the dependency injection container creates exactly one instance per application lifecycle.

# BackEnd design
