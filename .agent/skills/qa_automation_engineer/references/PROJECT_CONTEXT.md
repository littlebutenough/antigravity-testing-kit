# Project Context

## Usage Instructions

This file contains the context information of the project under test. **The Agent must read this file before starting any automation task** to correctly understand the domain and tech stack.

> ⚠️ **You need to update this file** for each specific project. Below is the template.

---

## Application Overview

- **Application Name:** [App Name]
- **Description:** [Brief description of the application]
- **Type:** [Web App / Mobile App / API]
- **Test Environment URL:** [Staging/Test URL]

## Tech Stack

- **Frontend:** [React / Angular / Vue / ...]
- **Backend:** [Java Spring / Node.js / .NET / ...]
- **Database:** [MySQL / PostgreSQL / MongoDB / ...]
- **Authentication:** [JWT / OAuth2 / Session-based / ...]

## Key Features & Modules

| Module | Description | Priority |
|--------|-------------|----------|
| Login | Login, forgot password, 2FA | High |
| Dashboard | Overview page | Medium |
| User Management | CRUD users, permissions | High |
| ... | ... | ... |

## Environment Details

| Environment | URL | Credentials |
|-------------|-----|-------------|
| Dev | ... | ... |
| Staging | ... | ... |
| Production | ... | N/A (do not test on prod) |

## Notes

- Add special notes about business rules, edge cases, or known issues here.
