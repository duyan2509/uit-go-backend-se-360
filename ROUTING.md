# 📚 Documentation Routing Guide

This document provides a comprehensive index of all markdown documentation files in the project, organized by category with descriptions for each file.

---

## 🏠 Root Documentation

### [README.md](README.md)
**Description:** Main project README providing getting started guide, prerequisites, and setup instructions. Includes information about Firebase Service Account Key setup, Docker Compose deployment, and CI/CD pipeline overview.

### [ARCHITECTURE.md](ARCHITECTURE.md)
**Description:** Comprehensive system architecture documentation (in Vietnamese) for the ride-hailing platform. Covers microservices architecture, core components, data storage schemas, data flows, scalability, security, and deployment strategies. Includes detailed explanations of User-service, Driver-service, Trip-service, Match-service, and Notification-service.

### [README_SELF_SERVICE.md](README_SELF_SERVICE.md)
**Description:** Self-Service Platform documentation covering CI/CD workflows, Terraform modules, GitHub Actions setup, and manual deployment procedures. Includes prerequisites, workflow inputs, and operational recommendations.

---

## 📋 Architecture Decision Records (ADRs)

### [adr/README.md](adr/README.md)
**Description:** Overview of Architecture Decision Records (ADRs) for the system. Provides ADR index table, status definitions, template for creating new ADRs, and links to related documentation.

### [adr/0001-architect.md](adr/0001-architect.md)
**Description:** ADR-0001 documenting the decision to adopt Microservices Architecture. Details the services (User, Driver, Trip, Matching, Notification), their technologies, and trade-offs.

### [adr/0002-grpc-rest.md](adr/0002-grpc-rest.md)
**Description:** ADR-0002 explaining the decision to use gRPC for internal service communication and REST for external APIs. Covers implementation details, protocol buffers, and consequences.

### [adr/0003-db-per-service.md](adr/0003-db-per-service.md)
**Description:** ADR-0003 documenting the Database per Service pattern implementation. Details database isolation, connection strings, data sharing strategies, and trade-offs.

### [adr/0004-move-catch-trip-to-trip-service.md](adr/0004-move-catch-trip-to-trip-service.md)
**Description:** ADR-0004 explaining the Domain-Driven Design decision to move trip acceptance functionality from Driver-service to Trip-service. Covers domain boundaries, implementation flow, and consequences.

### [adr/0005-redis-schema-refactor.md](adr/0005-redis-schema-refactor.md)
**Description:** ADR-0005 (Proposed) documenting a proposed refactoring of Redis schema for driver location tracking. Details current schema issues, proposed new schema, migration strategy, and affected services.

### [adr/0006-modularize-terraform.md](adr/0006-modularize-terraform.md)
**Description:** ADR-0006 documenting the decision to modularize Terraform infrastructure code. Covers module structure, design principles, implementation details, and current module status.

### [adr/0007-service-yaml-automation.md](adr/0007-service-yaml-automation.md)
**Description:** ADR-0007 documenting the service.yml configuration system and automation. Details the service configuration format, GitHub Actions integration, Terraform automation, and self-service capabilities.

---

## 🧪 Testing Documentation

### [test/main-flow/README.md](test/main-flow/README.md)
**Description:** Testing guide for ride-hailing system flow. Includes instructions for seeding test data (70 passengers, 20 drivers), running k6 load tests, and prerequisites for smoke/load testing.

---

## 💰 Cost Management Documentation

### [cost-optimization-simulation/README.md](cost-optimization-simulation/README.md)
**Description:** Comprehensive guide for the cost optimization simulation tool. Explains how to compare costs across Azure Container Apps, Azure Container Instances, and Azure Kubernetes Service. Includes setup instructions, usage examples, methodology, and troubleshooting.

### [docs/cost-management/README.md](docs/cost-management/README.md)
**Description:** Azure Cost Management documentation (equivalent to AWS Cost Explorer/Budgets). Covers cost analysis, tagging standards, budget setup (10 USD/month with 50/80/100% alerts), and Terraform architecture for cost management.

### Cost Optimization Reports
**Location:** `cost-optimization-simulation/results/report-*/comparison-report.md`
**Description:** Generated comparison reports from cost optimization simulations. These are timestamped reports showing cost comparisons between different Azure container platforms with detailed breakdowns and charts.

---

## 🚀 Platform & Self-Service Documentation

### [docs/platform/README.md](docs/platform/README.md)
**Description:** Main overview of the Self-Service Platform. Provides navigation to demo guides, architecture documentation, and quick links to key resources.

### [docs/platform/demo/README.md](docs/platform/demo/README.md)
**Description:** Demo and presentation guide index for the Self-Service Platform. Links to detailed demo guides, troubleshooting, presentation outlines, and demo scripts.

### [docs/platform/demo/DEMO_GUIDE.md](docs/platform/demo/DEMO_GUIDE.md)
**Description:** Comprehensive step-by-step demo guide starting from scratch (no Azure infrastructure). Covers Terraform backend setup, infrastructure provisioning, service creation, deployment, and verification. Includes detailed explanations for each step.

### [docs/platform/demo/CHECKLIST.md](docs/platform/demo/CHECKLIST.md)
**Description:** Pre-demo and demo-day checklist. Covers prerequisites, Azure setup, GitHub configuration, test runs, and demo phases.

### [docs/platform/components/README.md](docs/platform/components/README.md)
**Description:** Components and architecture overview for the Self-Service Platform. Links to detailed architecture documentation and lists key components (Terraform modules, GitHub workflows, scripts).

### [docs/platform/components/ARCHITECTURE.md](docs/platform/components/ARCHITECTURE.md)
**Description:** Detailed architecture documentation for the Self-Service Platform. Covers deployment flow, Terraform modules structure, security architecture, monitoring, scaling strategy, and rollback procedures.

---

## 🏗️ Infrastructure Documentation

### [azure-infra/README.md](azure-infra/README.md)
**Description:** Azure Infrastructure deployment guide using Terraform. Covers prerequisites, setup steps, Docker image building, infrastructure deployment, file structure, variables, and secrets management.

### [azure-infra/QUICKSTART.md](azure-infra/QUICKSTART.md)
**Description:** Quick start guide for Azure infrastructure deployment. Provides condensed step-by-step commands for login, initialization, building images, and deploying infrastructure.

---

## 🔧 Service Documentation

### [demo-service/README.md](demo-service/README.md)
**Description:** Documentation for the demo-service, an auto-generated service example. Includes development instructions, configuration guide, deployment information, and health check details.

---

## 📊 Documentation Statistics

- **Total Markdown Files:** 29
- **Architecture Decision Records:** 7
- **Platform Documentation:** 6
- **Infrastructure Documentation:** 2
- **Testing Documentation:** 1
- **Cost Management Documentation:** 2
- **Service Documentation:** 1
- **Root Documentation:** 3
- **Generated Reports:** Multiple (in results directories)

---

## 🔍 Quick Navigation

- **Getting Started:** [README.md](README.md)
- **System Architecture:** [ARCHITECTURE.md](ARCHITECTURE.md)
- **Architecture Decisions:** [adr/README.md](adr/README.md)
- **Self-Service Platform:** [docs/platform/README.md](docs/platform/README.md)
- **Infrastructure Setup:** [azure-infra/README.md](azure-infra/README.md)
- **Cost Optimization:** [cost-optimization-simulation/README.md](cost-optimization-simulation/README.md)

---

**Last Updated:** Generated automatically
**Project:** UIT Go Backend SE360

