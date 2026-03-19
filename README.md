# SAP S/4HANA Executive Dashboard

> **Production-ready SAP UI5 monorepo** — C-Level executive dashboard connected to live S/4HANA OData APIs, Cargo logistics tracking, and a reusable UI5 library for delay monitoring, Monte Carlo simulation, and configurable business rules.

[![SAP UI5](https://img.shields.io/badge/SAP%20UI5-1.120-blue?logo=sap)](https://ui5.sap.com)
[![BTP](https://img.shields.io/badge/SAP%20BTP-Cloud%20Foundry-0070F2?logo=sap)](https://www.sap.com/products/technology-platform.html)
[![License](https://img.shields.io/badge/license-Apache%202.0-green)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)](CONTRIBUTING.md)

---

## What's in This Repository

This monorepo contains two packages that work independently or together:

| Package                                                        | Description                                                       | Deploy         |
| -------------------------------------------------------------- | ----------------------------------------------------------------- | -------------- |
| [`packages/control-tower`](packages/control-tower)             | SAP UI5 CEO Control Tower — live S/4HANA + TrackCargo             | BTP CF App     |
| [`packages/logistics-delay-lib`](packages/logistics-delay-lib) | Reusable UI5 library — delay monitoring, rules engine, simulation | BTP HTML5 Repo |

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                      GitHub Repository                              │
│                                                                     │
│  packages/control-tower          packages/logistics-delay-lib      │
│  ├── webapp/                     ├── src/                           │
│  │   ├── service/                │   ├── control/                   │
│  │   │   ├── S4HanaService.js    │   │   ├── DelayMonitor.js        │
│  │   │   └── TrackCargoService.js│   │   ├── RulesEditor.js         │
│  │   ├── controller/             │   │   └── DelaySimulatorControl.js│
│  │   ├── view/                   │   ├── rules/RulesEngine.js        │
│  │   └── model/                  │   └── simulation/               │
│  ├── approuter/                  │       └── DelaySimulationEngine.js│
│  ├── mta.yaml                    ├── demo/                          │
│  └── xs-security.json           └── mta.yaml                       │
└─────────────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
    SAP BTP Cloud Foundry             External APIs
    ├── App Router (XSUAA)            ├── Cargo REST API
    ├── HTML5 App Repository          │   Cargo/developer
    ├── Destination Service           └── SAP S/4HANA OData v2
    └── XSUAA                             ├── MM_PUR_PO_MAINT_V2_SRV
                                          ├── SD_F1493_SALESORDER_SRV
                                          ├── API_BUSINESS_PARTNER
                                          └── SD_INVOICE_POST_SRV
```

---

## Quick Start

```bash
# Clone the repository
git clone https://github.com/YOUR-ORG/sap-s4hana-ceo-platform.git
cd sap-s4hana-ceo-platform

# Install root tooling
npm install

# Start the Control Tower locally (proxies to your S/4HANA system)
cd packages/control-tower
npm install && npm start

# Start the Library demo locally
cd packages/logistics-delay-lib
npm install && npm start
```

See the [How-To Guide](docs/HOW_TO_GUIDE.md) for full setup and deployment instructions.

---

## Repository Structure

```
sap-s4hana-ceo-platform/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                    ← Lint + build on every PR
│   │   └── deploy.yml                ← Deploy to BTP on merge to main
│   └── ISSUE_TEMPLATE/
│       ├── bug_report.md
│       └── feature_request.md
├── docs/
│   ├── HOW_TO_GUIDE.md               ← Complete team how-to guide ★
│   ├── ARCHITECTURE.md               ← System design decisions
│   ├── API_REFERENCE.md              ← All OData + Cargo endpoints
│   ├── RULES_AUTHORING.md            ← Writing business rules
│   └── CONTRIBUTING.md               ← How to contribute
├── packages/
│   ├── control-tower/                ← CEO Dashboard (SAP UI5 App)
│   └── logistics-delay-lib/          ← Reusable UI5 Library
├── .gitignore
├── .eslintrc.json
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
└── README.md                         ← You are here
```

---

## Features at a Glance

### Control Tower

- **8 live KPIs** — Revenue, Procurement Spend, Open POs, Overdue Deliveries, Sales Orders, OTD Rate, Active Suppliers, Gross Margin
- **5 tabs** — Overview · Procurement · Sales & Revenue · Suppliers · Logistics · Alerts
- **Live S/4HANA data** — direct `sap.ui.model.odata.v2.ODataModel` bindings, zero mock data
- **Cargo integration** — sea + air freight delay tracking via BTP Destination proxy
- **Excel export** — all tables exportable via `sap.ui.export.Spreadsheet`
- **Role-based access** — XSUAA with `Control_Tower_CEO` and `Control_Tower_Admin` roles

### Logistics Delay Library

- **10 built-in rules** covering overdue sea/air, high-value shipments, customs holds, trade lane risk
- **17 rule operators** — `eq`, `neq`, `gt`, `between`, `in`, `regex`, `isNull`, and more
- **7 simulation scenarios** — Baseline, PortCongestion, WeatherDisruption, CustomsDelay, CapacityShortage, Strike, Geopolitical
- **P50/P90/P99 estimates** with cost impact (demurrage + expedite)
- **Drop-in XML usage** — `<delaylib:DelayMonitor>`, `<delaylib:RulesEditor>`, `<delaylib:DelaySimulator>`

---

## Prerequisites

| Tool                  | Version | Purpose                                                  |
| --------------------- | ------- | -------------------------------------------------------- |
| Node.js               | ≥ 18    | Build tooling                                            |
| `@ui5/cli`            | ≥ 3     | `npm install -g @ui5/cli`                                |
| `mbt`                 | ≥ 1.2   | MTA build — `npm install -g mbt`                         |
| Cloud Foundry CLI     | ≥ 8     | BTP deployment                                           |
| SAP BTP Trial Account | —       | [hanatrial.ondemand.com](https://hanatrial.ondemand.com) |
| SAP S/4HANA system    | 2022+   | OData source (Private or on-prem)                        |
| Cargo API Key         | —       | API Key & Endpoints of Cargo tracking API                |

---

## Documentation

| Document                                      | Description                                      |
| --------------------------------------------- | ------------------------------------------------ |
| [HOW_TO_GUIDE.md](docs/HOW_TO_GUIDE.md)       | **Start here** — end-to-end team guide           |
| [ARCHITECTURE.md](docs/ARCHITECTURE.md)       | System design, data flows, BTP services          |
| [API_REFERENCE.md](docs/API_REFERENCE.md)     | All OData endpoints, TrackCargo endpoints        |
| [RULES_AUTHORING.md](docs/RULES_AUTHORING.md) | Writing and testing business rules               |
| [CONTRIBUTING.md](CONTRIBUTING.md)            | Branching strategy, PR process, coding standards |
| [CHANGELOG.md](CHANGELOG.md)                  | Version history                                  |

---
