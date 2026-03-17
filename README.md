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

## 1.5 Layered design:

## 1.6 Design patterns:

# BackEnd design
