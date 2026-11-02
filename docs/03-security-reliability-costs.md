# AWS Security, Reliability & Cost Evaluation

**Project:** Secure & Scalable Customer Platform on AWS

**Client:** Fictional UK-Based Online Retailer

**Primary Region:** Europe (London) — eu-west-2

**Architecture:** Serverless Cloud-Native Application

**Assessment Date:** November 2026

**Status:** Proposed Architecture — Not Deployed or Performance Tested

---

## 1. Executive Summary

This assessment evaluates the security, reliability, scalability, operational monitoring and cost implications of the proposed AWS customer platform.

The architecture uses Amazon CloudFront, Amazon S3, Amazon Cognito, Amazon API Gateway, AWS Lambda and Amazon DynamoDB.

The evaluation focuses on five engineering priorities:

1. Protect customer accounts and information.
2. Ensure application components have appropriate access permissions.
3. Maintain service continuity during failures.
4. Support changing customer demand.
5. Control operational expenditure using transparent cost assumptions.

The recommendations follow AWS Well-Architected principles and remain subject to implementation, testing and commercial validation.

## 2. Security Architecture

### 2.1 Identity and Access Management

The architecture separates customer identities from AWS resource permissions.

**Customer Authentication — Amazon Cognito**

Amazon Cognito manages customer identities and issues authentication tokens.

Recommended controls:

- Secure customer registration and authentication.
- Appropriate password and account recovery policies.
- Multifactor authentication where required by risk assessment.
- Short-lived access tokens and secure session handling.
- Account protection and monitoring.

**API Authorisation — Amazon API Gateway**

API Gateway HTTP API JWT authorisation is proposed to validate customer access tokens.

Routes should require appropriate OAuth scopes and validate the configured token issuer and audience.

**Resource Authorisation — AWS IAM**

AWS IAM controls which resources the Lambda application can access.

The Lambda execution role should permit only the DynamoDB actions necessary for the application.

**Security Principle:** Customer authentication does not replace application-level authorisation or least-privilege AWS permissions.

### 2.2 Data Protection

| Data Asset | Security Requirement | Proposed Control |
| --- | --- | --- |
| Customer accounts | Secure authentication | Amazon Cognito |
| API requests | Protected communication | HTTPS and token validation |
| Order records | Restricted read/write access | IAM and application-level ownership checks |
| Frontend files | Prevent unauthorised origin access | Private S3 bucket and CloudFront OAC |
| Application logs | Prevent unnecessary disclosure | Restricted CloudWatch permissions and log retention |
| Configuration secrets | Avoid hard-coded credentials | Managed secret storage where required |

The application must derive the customer's identity from verified authentication claims rather than trusting a user ID supplied in a request.

The frontend S3 bucket should remain private and use a regular S3 origin with CloudFront Origin Access Control.

### 2.3 Application and API Security

Recommended controls include:

- Input validation for order submissions.
- Maximum request payload sizes and appropriate API throttling.
- Customer-level authorisation checks.
- Generic error messages that do not expose internal details.
- Secure handling of authentication tokens.
- Logging of operational errors without exposing secrets or unnecessary personal information.
- Protection against duplicate order submissions.

A client-generated idempotency key and conditional database writes should be considered to prevent duplicate orders during retries.

### 2.4 Threat Assessment

| Threat | Potential Impact | Recommended Mitigation |
| --- | --- | --- |
| Account compromise | Unauthorised customer access | Cognito authentication controls and MFA where appropriate |
| Broken object-level authorisation | Exposure of another customer's orders | Backend ownership checks |
| Excessive API requests | Resource exhaustion and increased costs | API Gateway throttling and monitoring |
| Overprivileged Lambda role | Unauthorised resource access | Least-privilege IAM permissions |
| Publicly accessible S3 origin | Uncontrolled access to website resources | Private bucket and CloudFront OAC |
| Duplicate order processing | Incorrect or duplicated order records | Idempotency and conditional writes |
| Sensitive information in logs | Data exposure | Log sanitisation, retention and restricted access |

The risks above are architectural threat scenarios, not confirmed vulnerabilities.

## 3. Reliability and Availability

### 3.1 Availability Objective

The business requirements propose a **99.9% availability target**.

This is a design objective, not a measured availability result or end-to-end service guarantee.

Meeting this target requires appropriate architecture, monitoring, failure handling and operational processes.

### 3.2 Service Resilience

| AWS Service | Reliability Consideration |
| --- | --- |
| CloudFront | Distributed content delivery and caching |
| Amazon S3 | Managed durable object storage |
| API Gateway | Managed regional API service |
| AWS Lambda | Managed compute with scaling and fault-tolerance capabilities |
| DynamoDB | Regional managed database infrastructure |
| Cognito | Managed customer identity service |

Although AWS manages the underlying infrastructure, application-level failures and service dependencies must still be addressed.

The initial design uses one primary AWS Region.

A complete AWS Region failure is not automatically covered by this architecture.

### 3.3 Failure Handling

**Scenario 1 — Lambda execution failure**

Recommended response:

- Record the failure in CloudWatch.
- Return an appropriate error to the client.
- Use bounded retries where appropriate.
- Ensure retries cannot create duplicate orders.

**Scenario 2 — DynamoDB request failure**

Recommended response:

- Return a controlled application error.
- Apply appropriate retry and backoff behaviour.
- Monitor throttling and failed database operations.
- Avoid reporting an order as successful until persistence is confirmed.

**Scenario 3 — Regional disruption**

Recommended response:

- Activate the documented incident response process.
- Communicate service status.
- Evaluate recovery options against approved business objectives.
- Restore service using tested procedures and available backups.

The initial architecture does not provide automatic cross-Region failover.

## 4. Backup and Disaster Recovery

DynamoDB Point-in-Time Recovery (PITR) is proposed for protecting order data.

PITR can provide continuous backups with configurable recovery periods of 1 to 35 days.

Recovery processes must be documented and tested before any production use.

| Recovery Requirement | Proposed Approach |
| --- | --- |
| Accidental order-data deletion | DynamoDB PITR |
| Application configuration recovery | Version-controlled deployment configuration |
| Frontend recovery | Republish approved frontend files |
| Service disruption | Monitoring, incident response and recovery procedures |
| Complete Regional failure | Separate disaster recovery design required |

**Recovery Time Objective (RTO):** Maximum acceptable time to restore service.

**Recovery Point Objective (RPO):** Maximum acceptable amount of data loss measured in time.

Specific RTO and RPO values must be agreed with business stakeholders.

PITR alone does not establish an end-to-end disaster recovery capability.

## 5. Scalability and Performance

### 5.1 Initial Workload Assumptions

| Variable | Assumption |
| --- | --- |
| Daily active customers | 1,000 |
| Average API requests per customer per day | 10 |
| Normal daily requests | 10,000 |
| Normal 30-day request volume | 300,000 |
| Promotional peak scenario | Up to 10 times normal daily demand |
| Peak-day request volume | 100,000 |

These values are hypothetical planning assumptions rather than observed production traffic.

### 5.2 Scaling Strategy

**AWS Lambda**

Lambda can scale execution capacity in response to demand, subject to concurrency and other service limits.

The proposed design should monitor:

- Concurrent executions.
- Function duration.
- Errors and throttling.
- Cold-start effects.
- Resource utilisation.

**Amazon DynamoDB**

On-demand capacity is proposed because request volumes may vary.

DynamoDB can adapt automatically to increased throughput, but sudden spikes beyond previously observed peaks may require capacity planning or pre-warming.

**Amazon CloudFront**

CloudFront caches eligible static content closer to users, reducing repeated retrieval from the S3 origin.

**Amazon API Gateway**

API Gateway provides managed API handling. Throttling limits should be configured to reduce the risk of uncontrolled usage and application overload.

### 5.3 Performance Validation

Before production deployment, testing should evaluate:

- Average and percentile API latency.
- Error rates under normal and peak demand.
- Lambda concurrency and throttling.
- Database request latency and capacity.
- Customer authentication performance.
- End-to-end order submission reliability.

No load or performance testing has been completed for this proposed architecture.

## 6. Observability and Operational Monitoring

Amazon CloudWatch is proposed for application monitoring.

| Monitoring Area | Proposed Metric |
| --- | --- |
| API availability | Request counts and error rates |
| API performance | Response latency |
| Lambda processing | Duration, invocation errors and throttles |
| Database health | DynamoDB request latency and throttling |
| Security | Authentication failures and relevant access events |
| Financial management | Usage trends and unexpected cost increases |

Recommended operational practices:

- Define alert thresholds based on business requirements.
- Configure appropriate log retention.
- Restrict access to logs and monitoring configuration.
- Establish incident escalation responsibilities.
- Periodically review error patterns and service utilisation.

CloudWatch monitoring is proposed and has not been configured in a live AWS environment.

## 7. Cost Evaluation

### 7.1 Commercial Objective

The architecture should support changing customer demand while keeping operational costs understandable and proportionate.

A serverless model reduces the need to manage continuously running application servers, but costs still depend on actual usage and configuration.

### 7.2 AWS Cost Components

| Service | Main Cost Drivers | Potential Optimisation |
| --- | --- | --- |
| CloudFront | Data transfer and request usage | Effective caching |
| S3 | Storage, requests and data retrieval | Remove unused assets and manage storage |
| Cognito | Monthly active users and selected features | Select only required identity features |
| API Gateway | API request volume | Efficient API design and throttling |
| Lambda | Requests, duration and memory allocation | Optimise execution efficiency |
| DynamoDB | Reads, writes, storage and optional recovery features | Efficient access patterns and capacity selection |
| CloudWatch | Log ingestion, storage, metrics and alarms | Log retention and controlled verbosity |

Additional services, such as AWS WAF, DNS, custom domains and security monitoring tools, may add costs if selected.

### 7.3 Cost-Modelling Scenarios

| Scenario | Monthly API Requests | Purpose |
| --- | --- | --- |
| Normal Demand | 300,000 | Estimate typical operating expenditure |
| Sustained High Demand | 3,000,000 | Evaluate a tenfold usage scenario |
| Variable Demand | Depends on promotional days | Assess changing workloads and billing sensitivity |

The sustained high-demand scenario assumes elevated traffic throughout the month; it does not represent a single promotional peak day.

### 7.4 Pricing Calculator Assessment

Use the AWS Pricing Calculator to estimate the costs of:

1. AWS Lambda
2. API Gateway HTTP API
3. DynamoDB on-demand capacity and storage
4. Amazon Cognito
5. Amazon S3
6. Amazon CloudFront
7. CloudWatch

Regional services should be evaluated using Europe (London), eu-west-2.

Global service pricing and data-transfer destinations must be modelled according to their relevant pricing rules.

An accurate estimate also requires assumptions about:

- Customer monthly active users.
- API request and response sizes.
- Lambda execution duration and memory allocation.
- DynamoDB item sizes and read/write patterns.
- Frontend storage and monthly data transfer.
- Log volumes and retention.
- Selected security features.

**Cost limitation:** No validated monthly AWS cost is claimed because these usage and configuration assumptions have not been fully priced in the AWS Pricing Calculator.

### 7.5 Cost Governance

Recommended controls include:

- AWS Budgets alerts.
- Cost allocation tags.
- Periodic Cost Explorer reviews.
- Monitoring for unexpected usage increases.
- API throttling and application safeguards.
- Removal of unused development resources.
- Review of managed-service costs against alternative architectures.

Budget alerts provide financial visibility but should not be treated as a guaranteed spending cap.

## 8. Architecture Trade-Offs

| Decision | Benefit | Limitation |
| --- | --- | --- |
| Serverless compute | Reduced server administration | Cold starts, concurrency limits and variable usage |
| Managed NoSQL database | Simplified operations and flexible capacity | Requires suitable access-pattern design |
| Managed customer identity | Avoids implementing authentication from scratch | Configuration complexity and identity-service dependency |
| Single AWS Region | Lower initial complexity | No automatic full-Region disaster recovery |
| CloudFront and S3 | Efficient frontend delivery | Requires correct cache and origin-access configuration |
| Usage-based pricing | Aligns certain charges with consumption | Costs can increase unexpectedly during traffic spikes |

## 9. AWS Well-Architected Evaluation

| Pillar | Proposed Design Evidence |
| --- | --- |
| Operational Excellence | Monitoring, documented responsibilities and operational procedures |
| Security | Cognito, JWT authorisation, least-privilege IAM and controlled data access |
| Reliability | Managed services, fault handling, backups and recovery planning |
| Performance Efficiency | CloudFront caching, Lambda scaling and DynamoDB on-demand capacity |
| Cost Optimisation | Usage-based services, cost modelling and financial monitoring |
| Sustainability | Managed infrastructure and avoiding unnecessary always-on resources |

The design has been reviewed conceptually against these pillars. No formal AWS Well-Architected Review has been performed.

## 10. Final Technical Assessment

A serverless AWS architecture is a reasonable candidate for the fictional retailer's initial requirements.

The design provides potential advantages in:

- Infrastructure simplicity.
- Flexible application scaling.
- Managed identity and access controls.
- Customer data protection.
- Operational visibility.
- Usage-based cost management.

However, successful production adoption would require:

1. Security configuration and testing.
2. Performance and load testing.
3. Verified cost estimates.
4. Backup and recovery testing.
5. Operational monitoring configuration.
6. Business approval of availability and recovery objectives.

**Final Recommendation:** Progress the proposed AWS architecture to a proof-of-concept stage, followed by structured validation against the agreed functional, security, performance and commercial requirements.

---

## 11. Technical References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/)
- [API Gateway JWT Authorisers](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-jwt-authorizer.html)
- [CloudFront Origin Access Control](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html)
- [AWS Lambda Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
- [DynamoDB On-Demand Capacity](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/on-demand-capacity-mode.html)
- [DynamoDB Point-in-Time Recovery](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Point-in-time-recovery.html)
- [AWS Pricing Calculator](https://calculator.aws/)

---

**Portfolio Disclaimer:** This is a design-focused architecture case study for a fictional organisation. All workloads, risks, costs and service objectives are hypothetical or proposed. No live AWS deployment, production security validation or measured service availability is claimed.