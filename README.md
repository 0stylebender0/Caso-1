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
The user enters their credentials and the system verifies their identity. If correct, the user gains access to the system. If not, the system indicates the credentials are invalid and allows the user to try again.

#### Load the folder
The user indicates the location of the folder containing the source documents. The system validates that the folder exists and contains recognizable files. The system then shows a summary of what it found: how many files and of what type. The user confirms they want to start the processing.

#### Progress monitoring
The system begins processing the documents. The user can see which stage the process is currently in: file reading, data extraction, template mapping. If an error occurs with any file, the system notifies the user without stopping the rest of the processing. When finished, the system notifies the user that the result is ready.

#### Obtaining the result
The user accesses the pre-filled DUA. They can review each field and see how confident the system is about the extracted information. If a field requires correction, the user edits it directly. Once satisfied with the review, the user downloads the final Word document.

#### Logout
The user ends their session. The system securely terminates the session and returns to the login screen.

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
The following stategies were also taken in consideration:

| Strategy | Year | Reusability | Internationalization | Responsiveness | Advantages | Disadvantages |
|---|---|---|---|---|---|---|
| **Angular Material + CDK** | 2016 | High – prebuilt component library, theme system | Angular i18n + CDK a11y | CDK BreakpointObserver + Flex Layout | Mature, Angular-native, consistent design system | Opinionated styling, heavy theming customization effort |
| **PrimeNG** | 2016 | High – 90+ prebuilt components, theming API | Built-in i18n config object | Built-in responsive grid per component | Fast development, feature-rich, minimal setup | Less control over styling, external dependency risk |
| **Standalone Components + SCSS Tokens** | 2022 | High – self-contained, no NgModule needed, SharedModule pattern | Angular i18n or ngx-translate | CSS Grid/Flexbox + manual media queries | Lightweight, full control, modern Angular standard | More initial boilerplate, no prebuilt UI components |
| **Nx Design System + Storybook** | 2018 | Very High – monorepo shared UI library, publishable libs | ngx-translate per library | Responsive utilities per component | Ideal for large teams, isolated component development | Overkill for small teams, steep learning curve |
| **Ionic + Angular** | 2013 | High – mobile-first components, cross-platform | Built-in i18n support | Native mobile responsiveness, adaptive layouts | Best for mobile/tablet targets | Designed for mobile apps, not web dashboards |


## 1.4 Security:

## 1.5 Layered design:

## 1.6 Design patterns:

# BackEnd design
