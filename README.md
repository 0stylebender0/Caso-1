# DUA Streamliner

---

## Problem

The preparation of the Single Administrative Document (DUA) is a manual, repetitive, and error-prone process that requires interpreting multiple source documents in diferent formats such as Excel, Word, PDF, and scanned images. This work heavily depends on the expert knowledge of customs agents, creating bottlenecks, high operational costs, and risk of rejections or fines from customs authorities. This project aims to automate this process through artificial intelligence, automatically extracting and mapping relevant information to the official template of the Ministry of Finance of Costa Rica.

This project aims to automate this process through artificial intelligence, automatically extracting and mapping relevant information to the official template of the Ministry of Finance of Costa Rica. The goal is not to replace the customs expert but to transform them into a strategic validator, eliminating manual transcription work and allowing them to focus on high-value tasks such as tariff classification optimization, regulatory compliance analysis, and exception handling. By reducing processing time from hours to minutes and minimizing human error, the system seeks to make Costa Rican foreign trade more efficient, competitive, and reliable.

---

## Authors

- Daniel Campos
- Kevin Espinoza

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

## 1.3 Component design strategy:

## 1.4 Security:

## 1.5 Layered design:

## 1.6 Design patterns:

# BackEnd design