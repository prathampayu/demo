# Consolidated Training and Work Progress Report at PayU

**Employee:** Pratham Ghorpade  
**Role:** Engineering Intern  
**Team:** Engineering Team  
**Organization:** PayU  
**Reporting period:** From the beginning of the internship work up to **13 November 2026**

## Introduction

During my internship at PayU, my work has primarily focused on understanding PayU’s payment and affordability ecosystem, especially EMI-related products and services. I worked across data analysis, monitoring, integrations, dashboards, alerting, agent architecture, and operational troubleshooting.

The work progressed from understanding the business and technical domain to defining analytical requirements, developing SQL and monitoring patterns, designing agent capabilities, and documenting implementation approaches for future integration.

This report is based on the work records and project documentation maintained during the internship. Where a query or design was documented but not independently executed or deployed, it is identified as a proposed or user-reported implementation rather than a confirmed production implementation.

---

# 1. Type of Training Undergone and Objectives

## 1.1 Organizational and domain training

The initial training involved understanding PayU’s business environment and the role of the Engineering Team in supporting payment products. I learned how payment infrastructure connects multiple stakeholders, including:

- Merchants
- KAMs
- Product teams
- Payment solutions and integration teams
- Banking and lender teams
- Banking Operations
- Technical support and Tech On-Call teams
- Internal engineering and platform teams

A significant part of this training focused on PayU’s affordability and EMI ecosystem. The main EMI products considered included:

- Credit Card EMI
- UPI Credit Card EMI
- Debit Card EMI
- Cardless EMI
- BNPL, including LazyPay-related flows

### Objectives

The objectives were to:

1. Understand how EMI transactions are initiated, processed, authorized, and completed.
2. Learn how merchant, lender, bank, payment gateway, and configuration layers interact.
3. Understand common EMI failure scenarios and their business impact.
4. Learn how engineering teams support KAMs and internal stakeholders with data-driven analysis.
5. Understand how production monitoring and operational alerts help detect issues early.

---

## 1.2 Technical training in data analysis and SQL

I underwent practical training in querying payment and EMI data using SQL. This included learning how to work with Redshift materialized views and transaction-related data.

The training covered:

- Filtering transactions by date and time
- Grouping data by merchant, lender, bank, payment gateway, and payment mode
- Calculating transaction volume and success rate
- Categorizing transaction statuses
- Joining transaction data with payment-type and gateway configuration data
- Extracting information from JSON fields
- Building queries for Grafana dashboards and alerts
- Handling dynamic lender information from pre-eligibility response data

### Objectives

The main objectives were to:

- Convert business questions into structured SQL queries.
- Create reusable query patterns instead of hardcoding individual cases.
- Ensure that transaction metrics use consistent status definitions.
- Support monitoring and investigation through reliable data analysis.
- Separate confirmed results from assumptions or unverified interpretations.

---

## 1.3 Monitoring, observability, and alerting training

Another important area of training involved Grafana, OpenSearch, Redshift, and operational monitoring.

The work covered:

- Grafana time-series panels
- Grafana Stat queries
- Business and infrastructure dashboards
- OpenSearch-based lender API monitoring
- Lucene query construction
- Success-rate and GMV monitoring
- File-processing monitoring
- SFTP failure monitoring
- Alert priorities and escalation paths

### Objectives

The objectives were to:

1. Detect technical and business issues proactively.
2. Create dashboards that help teams understand the condition of EMI services.
3. Monitor performance separately at merchant, lender, bank, gateway, and API levels.
4. Route alerts to the correct team based on severity and ownership.
5. Ensure alerts provide meaningful calculated values rather than generic labels.

---

## 1.4 Training in agentic AI and automation architecture

I also gained practical exposure to designing AI-based internal support and analysis systems.

This included understanding:

- Intent classification
- Skill-based routing
- Read-only database analysis
- OpenSearch transaction investigation
- Documentation and wiki lookup
- Planner-executor architectures
- Result verification
- Cache and idempotency
- Redis-based task state
- FastAPI A2A sidecars
- Structured agent responses
- Evidence-based failure analysis

The EMI KAM Agent was defined as a focused internal assistant for KAMs, Product, PS, Integration, and other internal teams. It is intended to support simple English and Hinglish and to provide business-friendly explanations.

### Objectives

The objectives were to:

- Design an agent that uses the correct data source for each question.
- Prevent unsupported conclusions when evidence is missing.
- Separate transaction debugging from aggregate business analysis.
- Protect customer information and sensitive configuration.
- Provide deterministic responses explaining what happened, why it happened, and what should happen next.

---

## 1.5 Training in debugging and engineering reliability

I also developed a structured approach for debugging repeated agent or system failures.

The four-phase debugging method included:

1. Capturing the failure state.
2. Diagnosing the most likely root-cause pattern.
3. Taking the smallest safe recovery action.
4. Producing a human-readable report containing the evidence, result, risk, and follow-up action.

### Objectives

The main objectives were to:

- Avoid blind and repeated retries.
- Distinguish logic failures from environment or state issues.
- Verify actual filesystem, branch, service, and data conditions.
- Make recovery actions reversible and limited in scope.
- Clearly identify whether the result was successful, partial, or blocked.

---

# 2. Technical Knowledge Gained

## 2.1 Understanding of EMI transaction analysis

I learned how to analyze EMI performance using transaction-level data.

The analysis framework includes:

- Total transaction volume
- Eligible EMI attempts
- Successful transactions
- Failed transactions
- Bounced transactions
- Dropped transactions
- User-cancelled transactions
- Success and failure rates
- Merchant-level performance
- Lender and issuer-level performance
- Payment gateway performance
- BIN-related failures
- Configuration-related failures

A key learning was that business performance and configuration issues must be kept separate. For example:

- A low success rate may result from lender rejection or authorization failure.
- Zero traffic may indicate an inactive mode or incomplete integration.
- BIN errors may indicate card eligibility or configuration problems.
- Missing parameters may prevent the EMI flow from being presented or completed.

These situations require different investigation paths.

---

## 2.2 Success-rate calculation and status handling

I learned that success-rate calculations must use clearly defined numerator and denominator rules.

The documented EMI success-rate patterns count statuses such as:

- `captured`
- `pending`
- `auth`
- `cancelled`
- `autoRefund`

However, some analysis requirements use different treatment for `pending`. In the North Star analysis definition, the intended successful outcome is based on captured transactions and failed-to-success reconciliation, while cancelled transactions remain a separate user-cancelled category.

I also learned that statuses such as:

- `in progress`
- `initiated`

should not automatically be included in the denominator for certain EMI success-rate calculations.

This reinforced the importance of checking:

- Status definitions
- Time range
- Data source
- Aggregation method
- Merchant or lender filters
- Denominator logic

before comparing two metrics.

---

## 2.3 Redshift and materialized-view analysis

I gained practical knowledge of PayU-related Redshift data structures and query patterns.

The work involved relations such as:

- `data_dumps.payubiz_payu_transaction_mv`
- `data_dumps.payubiz_payu_payment_type_mv`
- `data_dumps.payubiz_payu_merchant_payment_type_mv`
- `data_dumps.payubiz_payu_paymentgateway_mv`
- `data_dumps.payubiz_payu_paymentgateway_param_mv`
- `data_dumps.treasury_aggregated_pre_eligibility_data_mv`
- `data_dumps.treasury_aggregated_emi_processed_file_mv`

I learned how to:

- Join transactions with payment-type mappings.
- Associate lender codes with bank names.
- Analyze active and inactive merchant mappings.
- Identify gateway-level configuration.
- Use `addedon` for transaction time-based analysis.
- Build monthly and rolling-window metrics.
- Create Grafana-compatible date filters.
- Avoid mixing unrelated views and business domains.

---

## 2.4 Pre-eligibility JSON analysis

One of the technically challenging areas was analyzing pre-eligibility responses stored as JSON.

The pre-eligibility data contains dynamic lender information under `response_data`. The work involved defining a method to:

- Parse the JSON response.
- Extract lender keys dynamically.
- Identify lender-level status.
- Extract `errorCode` and `errorReason`.
- Extract `journeyType` where available.
- Count TRUE and FALSE eligibility responses.
- Group results by merchant, lender, journey type, and tenure.
- Support all merchants and all lenders rather than hardcoding a single merchant or lender.

The intended dashboards also include filters for:

- Merchant ID
- Amount
- Response time
- Requesting API
- Grafana time range

A key technical learning was that dynamic JSON fields must be validated against actual returned records. A proposed JSON path or query structure cannot be considered fully verified without checking the extracted values against the active datasource.

---

## 2.5 Integration and routing analysis

I learned how EMI integrations can be analyzed across multiple configuration layers.

The work included:

- Payment-type configuration
- Merchant-payment-type mapping
- Payment gateway configuration
- Gateway active status
- Lender-to-gateway mapping
- Payment gateway parameters
- PIH-related configuration

The documented PIH classification logic considers:

- Active `flowtype` parameters
- Active `posturl` parameters
- Legacy `integrationType = 'O'`
- Special treatment for PG `281` as the EMI MS base PG

The integration queries were designed to identify:

- Inactive lender integrations
- Inactive merchant mappings
- Fully active lender and gateway combinations
- PIH-capable payment gateways
- Direct non-PIH payment gateways

These queries represent defined analysis patterns. The current records do not independently establish the live state of every integration or confirm that a production dashboard has been deployed.

---

## 2.6 Grafana and OpenSearch knowledge

I gained experience designing monitoring requirements for Grafana and OpenSearch.

### Grafana work included:

- Time-series success-rate panels
- Merchant-specific metrics
- Lender-wise success rates
- Bank-wise Credit Card EMI reporting
- Monthly EMI success-rate reporting
- GMV monitoring
- Transaction volume monitoring
- Pre-eligibility dashboards
- Processed-file monitoring
- Stat queries
- Unified alert-rule concepts

### OpenSearch work included:

- Lender API latency visualizations
- Lucene query requirements
- API-specific monitoring
- Error and latency investigation
- Monitoring lender flows such as HDFC DC EMI, LazyPay BNPL, Bajaj, LPEMI, Axio, and ZestMoney

The monitoring inventory included APIs such as:

- Pre-eligibility
- Create order
- Validate OTP
- Order confirmation
- Status inquiry
- Initiate transaction
- Customer status
- Eligibility
- Inquiry

The documented alert requirements specify P0 priority and Tech On-Call escalation for certain latency cases, although exact numeric latency thresholds were not available in all cases.

---

## 2.7 Agent architecture knowledge

I gained knowledge of how a production agent can be structured into several independent components.

The documented UPI agent architecture includes:

- Streamlit reference application
- FastAPI A2A production sidecar
- Redis task state
- JSON-RPC and REST endpoints
- Intent routing
- Skill dispatching
- Planning and execution
- Database tools
- OpenSearch tools
- Documentation lookup
- Intent verification
- Result caching
- Idempotency
- Session locking
- SSE progress streaming
- Result storage
- Evaluation and feedback scoring

I also learned that different types of questions should use different evidence sources:

| Question type | Preferred source |
|---|---|
| Transaction-specific investigation | OpenSearch logs |
| Aggregate metrics | Redshift |
| Conceptual or documentation questions | Local wiki first, then documentation system |
| Integration or configuration analysis | Approved configuration and data sources |

This architecture helped establish the importance of narrow evidence selection and controlled fallback behavior.

---

## 2.8 CodeGraph and knowledge-indexing concepts

I also explored a proposed CodeGraph architecture for the EMI microservice.

The proposed design included:

- Offline repository parsing
- GitLab CI-based artifact generation
- Tree-sitter parsing
- Structured extraction of error codes, lender mappings, feature flags, and configuration
- SQLite with FTS5 for full-text and relationship search
- Protobuf for fast exact-key lookups
- Bounded query responses with source references
- Artifact validation before publishing

I also compared this with a Nexus-style pre-indexed architecture involving:

- Neo4j knowledge graphs
- Qdrant or Weaviate vector databases
- Offline code parsing
- Call graphs
- Git history analysis
- Embeddings
- Shared knowledge access for agents

The important learning was the difference between:

- A focused EMI-specific CodeGraph artifact.
- A broader organization-wide pre-indexed knowledge platform.

The records describe these as architectural proposals. They do not confirm that the artifacts, runtime query CLI, or Nexus integration have been deployed.

---

# 3. Understanding of the Organization

## 3.1 Understanding PayU’s business environment

Through the work, I developed an understanding that PayU operates a complex payment ecosystem involving:

- Merchants
- Payment modes
- Banks and lenders
- Payment gateways
- Merchant configurations
- Eligibility systems
- Transaction processing services
- Monitoring and support systems

EMI is not a single isolated product. It depends on several connected layers:

1. Merchant configuration.
2. EMI payment-type activation.
3. Lender and bank eligibility.
4. Payment gateway routing.
5. Transaction processing.
6. Authentication and authorization.
7. Reconciliation and final status handling.
8. Monitoring and operational support.

A failure at any one of these layers can affect the customer experience and merchant performance.

---

## 3.2 Understanding of team responsibilities

I gained an understanding of how different teams contribute to EMI operations.

### KAMs

KAMs require business-friendly analysis of:

- Merchant performance
- Success-rate changes
- Lender failures
- Volume drops
- Customer or merchant impact
- Recommended next steps

### Product and PS teams

These teams help identify:

- Product behavior
- Expected EMI journeys
- Merchant onboarding requirements
- Lender-specific product conditions
- Integration gaps

### Integration and Banking Operations teams

These teams are involved in:

- Lender activation
- Merchant-payment mapping
- Gateway routing
- SFTP configuration
- File-sharing problems
- Bank and lender coordination

### Engineering and Tech On-Call teams

These teams handle:

- Service failures
- API latency
- Transaction errors
- Production incidents
- Infrastructure issues
- Alert investigation
- Corrective technical action

This helped me understand that technical analysis is useful only when it leads to a clear action for the correct stakeholder.

---

## 3.3 Understanding of operational monitoring

I learned that PayU monitoring needs to cover both technical and business performance.

### Technical monitoring

Examples include:

- API latency
- Service errors
- OpenSearch log patterns
- Unhealthy pods
- CPU and memory utilization
- File-processing failures
- SFTP connection failures

### Business monitoring

Examples include:

- EMI success rate
- Transaction volume
- EMI GMV
- Active merchant count
- Pre-eligibility hit volume
- Bank-wise performance
- Lender-wise performance
- BIN eligibility failures

A technically healthy service can still have a serious business problem if transaction success rate or lender performance drops. Therefore, both monitoring categories must be maintained separately but analyzed together when required.

---

## 3.4 Understanding of evidence, data ownership, and security

A major organizational learning was the importance of using the correct source for each type of information.

For example:

- Redshift is appropriate for aggregate transaction and business metrics.
- OpenSearch is appropriate for transaction timelines and service-level debugging.
- Configuration views are required for integration and routing analysis.
- Documentation or wiki sources are required for conceptual and process-related questions.

I also learned that analysis systems should:

- Remain read-only for operational data.
- Avoid exposing customer PII.
- Avoid exposing credentials or secrets.
- Clearly distinguish retrieved facts from assumptions.
- Refuse or request clarification when evidence is insufficient.

---

# 4. Progress of Work and Implementation Done

## 4.1 Initial domain familiarization

The first stage involved understanding:

- PayU’s payment ecosystem
- EMI products
- Lender and gateway relationships
- Merchant and integration concepts
- Common operational issues
- Teams involved in EMI support

This created the foundation for the later SQL, monitoring, and agent work.

---

## 4.2 EMI business analysis framework

A structured North Star EMI analysis approach was defined.

The framework includes:

- Merchant-specific analysis using Merchant ID or Merchant Key
- Default analysis of the last month when no period is specified
- Comparison with the previous two-month average
- Separate reporting of success, failed, bounced, dropped, and user-cancelled buckets
- Drill-down by Field 9 error reason
- Lender, issuer, bank, PG, and BIN analysis
- Separate treatment of configuration issues
- One or two anonymized transaction examples where appropriate
- Business-friendly next actions

This created a consistent analysis method for KAMs and internal support teams.

---

## 4.3 Success-rate and business metric query work

Query patterns were developed or documented for:

- Merchant-wide EMI success rate
- Lender-specific success rate
- Bank-wise Credit Card EMI success rate
- Payment-gateway-specific performance
- Monthly EMI success rate
- Monthly EMI and BNPL GMV
- EMI transaction volume
- Low success-rate alerts
- Low GMV alerts
- Merchant count and transaction count alerts

The work also included documenting important query limitations, such as differences in:

- Denominator definitions
- Status exclusions
- Time windows
- Lender filters
- Aggregation levels
- Merchant-wide versus lender-specific calculations

---

## 4.4 Pre-eligibility dashboard work

The pre-eligibility dashboard requirements were developed using the preferred source:

`data_dumps.treasury_aggregated_pre_eligibility_data_mv`

The work included:

- All-lender status reporting
- Merchant-to-lender grouping
- TRUE and FALSE eligibility counts
- Dynamic lender extraction
- Journey-type analysis
- Tenure-related grouping
- Grafana time-picker filtering
- Merchant-ID dropdown requirements
- Amount and response-time filters
- Requesting API filtering

The user-reported all-lender query was described as working without the journey-type condition. However, the journey-type filter returned no data in one reported test, so the exact extraction path and availability of journey-type values still require validation against actual records.

---

## 4.5 Integration inventory and routing analysis

Query patterns were documented for:

- Inactive EMI integrations
- Inactive merchant mappings
- Fully active lender and gateway mappings
- PIH-capable gateway classification
- Direct non-PIH gateway classification
- EMI MS base PG identification
- Payment-type and gateway status analysis

The work used the payment-type, merchant-payment-type, payment-gateway, and gateway-parameter materialized views.

This helped define a repeatable way to identify whether an EMI integration is:

- Active
- Inactive
- PIH-capable
- Direct non-PIH
- Associated with the EMI MS base gateway

---

## 4.6 Monitoring and alert specifications

A broad monitoring and alerting inventory was prepared.

### Business alerts covered:

- EMI success-rate drops
- EMI GMV drops
- Transaction-volume drops
- Merchant-count drops
- Pre-eligibility hit drops
- BIN eligibility failures
- Bank-wise Credit Card EMI issues
- Lender-specific performance issues
- Monthly EMI success-rate reporting

### Technical and operational alerts covered:

- Lender API latency
- OpenSearch-based API monitoring
- Processed-file counts
- Missing or failed files
- SFTP connection failures
- Invalid credentials or passwords
- File-processing error reasons

For processed-file monitoring, the source identified was:

`data_dumps.treasury_aggregated_emi_processed_file_mv`

The work also documented Asia/Kolkata reporting-day boundaries and separate requirements for count notifications, comparison alerts, and SFTP-related errors.

These alert definitions are documented specifications. The current records do not independently confirm that all Grafana rules, notification routes, or production alert configurations are active.

---

## 4.7 EMI and UPI agent architecture work

The agent work progressed from defining business skills to designing a production-style execution architecture.

The EMI KAM Agent was scoped around five major capabilities:

1. North Star database analysis.
2. OpenSearch transaction debugging.
3. EMI FAQ support.
4. EMI integration guidance.
5. Configuration and onboarding guidance.

The agent is intended to:

- Understand simple English and Hinglish.
- Ask for Merchant ID or Merchant Key where necessary.
- Use read-only approved data sources.
- Avoid customer PII and secrets.
- Explain what happened, why it happened, and what should happen next.
- Keep payment performance separate from configuration diagnostics.
- Provide deterministic, business-friendly responses.

The related UPI agent architecture included:

- Request routing
- Skill dispatch
- Database analysis
- OpenSearch investigation
- Wiki and documentation lookup
- Redis task management
- A2A APIs
- Polling and streaming
- Intent verification
- Cache and idempotency
- Controlled remediation
- Result storage and evaluation

The architecture has been designed and documented. The current records do not establish that every described component has been deployed or that the full production runtime has been independently tested.

---

## 4.8 CodeGraph and offline knowledge-layer work

A proposed offline knowledge layer was defined for the EMI microservice.

The expected design includes:

- Repository analysis outside the live request path.
- SQLite/FTS5 graph storage.
- Protobuf fast-path lookup artifacts.
- GitLab CI integration.
- Structured source extraction.
- Validation and artifact integrity checks.
- Read-only querying with bounded source references.

The relationship between the EMI-specific CodeGraph and a broader Nexus-style graph/vector system was also analyzed.

The current status is architectural and planning-oriented. The project records explicitly do not prove that the binary artifact, reader CLI, or runtime integration has been completed.

---

## 4.9 Agent debugging and reliability process

A reusable agent-introspection debugging method was documented.

The process helps diagnose:

- Repeated tool calls
- Context overflow
- Incorrect routing
- Missing files
- Stale repository state
- Service connection failures
- Timeout and quota issues
- Failed tests after claimed fixes
- Environment mismatches

The process emphasizes:

- Evidence collection
- Smallest contained recovery
- Avoiding blind retries
- Clear success or blocked status
- Documented follow-up actions

This provides a disciplined approach for improving reliability in agentic systems.

---

# Overall Status Summary

| Work area | Progress/status |
|---|---|
| PayU EMI domain understanding | Completed as practical domain training |
| EMI business analysis framework | Defined and documented |
| Redshift SQL and metric patterns | Developed and documented |
| EMI success-rate and GMV monitoring | Query patterns and alert requirements documented |
| Pre-eligibility analysis | Dashboard requirements and dynamic extraction patterns defined |
| Integration and routing inventory | Query patterns and classification rules documented |
| Grafana and OpenSearch monitoring | Monitoring inventory and query requirements documented |
| Processed-file and SFTP monitoring | Source and alert requirements documented; production activation not confirmed |
| EMI KAM Agent scope | Defined with five major skills |
| UPI agent architecture | Detailed execution and deployment architecture documented |
| CodeGraph/Nexus architecture | Proposed and compared; implementation not independently confirmed |
| Agent failure-debugging workflow | Defined and documented |
| Production deployment status | Not independently verified from the available records |
| Active Grafana alerts and notification routes | Not independently verified from the available records |

---

# Conclusion

The internship work has provided broad exposure to PayU’s EMI and payment ecosystem. The work moved from foundational domain learning to practical technical analysis, monitoring design, integration investigation, and agent architecture.

The main outcomes have been:

- Understanding EMI transaction and integration flows.
- Building structured Redshift query patterns.
- Defining success-rate, GMV, volume, and eligibility metrics.
- Designing Grafana and OpenSearch monitoring requirements.
- Analyzing lender, gateway, merchant, BIN, and configuration relationships.
- Defining the scope and behavior of an EMI KAM Agent.
- Understanding production agent architecture and task execution.
- Designing an offline CodeGraph and knowledge-indexing approach.
- Establishing a reliable, evidence-based debugging methodology.

The work completed so far has created a strong foundation for future implementation. The remaining validation areas are primarily execution-related: confirming queries against active datasources, validating dynamic JSON fields, configuring dashboards and alerts in Grafana, testing agent runtime behavior, and verifying any generated CodeGraph artifacts or production integrations.
