# AWS Customer Platform — Solution Requirements

**Project:** Secure & Scalable Customer Platform on AWS

**Engagement Type:** Cloud Solutions Architecture Case Study

**Client:** Fictional UK-Based Online Retailer

**Cloud Provider:** Amazon Web Services (AWS)

**Proposed Primary Region:** Europe (London) — eu-west-2

**Status:** Architecture Design — No Production Deployment

---

## 1. Business Scenario

A growing UK-based online retailer requires a cloud-hosted platform through which customers can securely access their accounts, submit orders and track order status.

Demand varies throughout the year, with significant increases expected during promotional campaigns and seasonal sales.

The organisation wants to modernise its customer-facing infrastructure while avoiding unnecessary server management, controlling operating costs and maintaining reliable service availability.

The objective is to design an AWS architecture that satisfies the retailer's requirements and provides a foundation for future growth.

## 2. Business Objectives

| ID | Objective | Business Outcome |
| --- | --- | --- |
| BO-01 | Deliver a reliable online service | Customers can access the platform consistently |
| BO-02 | Support fluctuating customer demand | The platform can respond to increases in usage |
| BO-03 | Protect customer information | Authentication and data-access controls are enforced |
| BO-04 | Minimise infrastructure administration | Managed cloud services reduce operational burden |
| BO-05 | Control expenditure | Running costs are measurable and aligned with usage |
| BO-06 | Improve operational visibility | Engineers can monitor service health and investigate failures |

## 3. Functional Requirements

| ID | Requirement | Priority |
| --- | --- | --- |
| FR-01 | Customers can access a web-based interface | High |
| FR-02 | Customers can register and authenticate securely | High |
| FR-03 | Authenticated customers can submit orders | High |
| FR-04 | Customers can view their own order status | High |
| FR-05 | Order records are stored persistently | High |
| FR-06 | Application errors and operational events can be monitored | High |

Payment processing, stock reservation and shipping-provider integrations are outside the initial project scope.

## 4. Non-Functional Requirements

| Category | Proposed Requirement |
| --- | --- |
| Availability | Target 99.9% service availability, subject to feasibility assessment |
| Scalability | Support changes in workload without manually provisioning servers |
| Performance | Define and validate customer-facing response-time targets |
| Security | Apply least privilege, secure authentication and encryption |
| Resilience | Account for individual service failures and recovery requirements |
| Observability | Provide application logs, metrics and operational alerts |
| Cost | Use a transparent, usage-based cost model where appropriate |
| Maintainability | Prefer managed services and clearly documented components |

All performance and availability targets are preliminary design assumptions, not tested service guarantees.

## 5. Illustrative Workload Assumptions

The following values are used for architecture and cost modelling only.

| Workload Variable | Initial Assumption |
| --- | --- |
| Daily active customers | 1,000 |
| Average API requests per active customer per day | 10 |
| Normal daily API requests | 10,000 |
| Promotional peak | Up to 10 times normal demand |
| Primary market | United Kingdom |
| Customer data | Synthetic data for any demonstration or testing |
| Hosting location | AWS Europe (London) Region |

The estimates will be refined before performing a cost comparison.

## 6. Architectural Constraints

The proposed design should:

- Use AWS managed services where appropriate.
- Separate customer-facing interfaces from application logic.
- Enforce appropriate authentication and authorisation.
- Avoid exposing the database directly to the public internet.
- Minimise unnecessary infrastructure administration.
- Consider resilience, monitoring and cost from the outset.
- Support future extension without requiring a complete redesign.

## 7. Solution Evaluation Criteria

The proposed architecture will be evaluated against:

1. Suitability for the business requirements.
2. Security and identity management.
3. Reliability and recovery capabilities.
4. Ability to handle variable demand.
5. Operational complexity and maintainability.
6. Cost efficiency and architectural trade-offs.
7. Alignment with AWS Well-Architected principles.

## 8. Project Scope

This project will produce:

- AWS solution architecture diagrams.
- Service selection and technical justifications.
- Security and IAM design.
- Reliability and scalability analysis.
- Monitoring and operational recommendations.
- Illustrative cost estimates.
- Architecture decision records and trade-offs.

The project is a design-focused portfolio case study. No live AWS deployment, successful load testing or production availability is claimed.

## 9. References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/)
- [AWS Serverless Multi-Tier Architectures](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/web-application.html)
- [AWS Pricing Calculator](https://calculator.aws/)