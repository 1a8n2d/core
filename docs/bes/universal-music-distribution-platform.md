# BES Universal Music Distribution Platform Architecture

## Purpose

This specification extends BES (Bot Evolution System) with an enterprise-grade Universal Music Distribution Platform that manages the full lifecycle of a digital music release, from artist onboarding through royalties, analytics, contracts, partner management, and administration.

## Target Value Stream

```mermaid
flowchart TD
  Artist --> Dashboard
  Dashboard --> ReleaseManager[Release Manager]
  ReleaseManager --> MetadataEngine[Metadata Engine]
  MetadataEngine --> RightsEngine[Rights Engine]
  RightsEngine --> ValidationEngine[Validation Engine]
  ValidationEngine --> DistributionEngine[Distribution Engine]
  DistributionEngine --> PlatformConnectors[Platform Connectors]
  PlatformConnectors --> AnalyticsEngine[Analytics Engine]
  AnalyticsEngine --> RoyaltyEngine[Royalty Engine]
  RoyaltyEngine --> KnowledgeEngine[Knowledge Engine]
  KnowledgeEngine --> ContractEngine[Contract Engine]
  ContractEngine --> PartnerPortal[Partner Portal]
  PartnerPortal --> Administration
```

## Core Domains

| Domain              | Responsibility                                                                                      | Primary Outputs                                              |
| ------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| Release Manager     | Release lifecycle, status orchestration, asset grouping, approvals                                  | Release records, lifecycle events, submission packages       |
| Metadata Manager    | Track, album, contributor, territory, language, explicit-content, and genre metadata                | Normalized metadata DTOs, DDEX-ready metadata                |
| Audio Validator     | Audio format, loudness, duration, codec, bitrate, duplication, silence, and corruption checks       | Audio validation report, remediation tasks                   |
| Artwork Validator   | Cover-art dimensions, color mode, forbidden content, text consistency, and platform policy checks   | Artwork validation report, policy exceptions                 |
| Rights Manager      | Ownership, licensing, territory restrictions, contributor shares, conflicts, takedowns              | Rights graph, clearance decisions, conflict alerts           |
| Distribution Engine | Delivery orchestration, packaging, retries, platform-specific submission state                      | Distribution jobs, delivery receipts, error recovery actions |
| Platform Connectors | API, SDK, DDEX, feed, SFTP, OAuth, and custom partner integrations                                  | Connector adapters, auth profiles, platform receipts         |
| Analytics Engine    | Streams, revenue, country, audience, growth, forecasting, recommendations                           | Analytics facts, dashboards, recommendations                 |
| Royalty Engine      | Royalty splits, statements, revenue share, commission, subscription, white-label, and hybrid models | Statements, payout calculations, audit trails                |
| Knowledge Engine    | Document ingestion, semantic extraction, graph updates, vector indexing, architecture suggestions   | BES records, graph edges, implementation tasks               |
| Contract Engine     | Contract analysis, legal-risk comparison, draft generation, appendices, commercial offers           | Contract drafts, risk checklists, human approval gates       |
| Partner Portal      | Partner onboarding, requirements exchange, documents, integration status                            | Partner profiles, requirement matrices, approvals            |
| Administration      | Roles, permissions, audit, settings, compliance, incident controls                                  | Admin policies, audit exports, security events               |

## Knowledge Processing Pipeline

Every incoming source is processed by the same controlled pipeline:

1. `RawDocumentReceived` stores source provenance, license, checksum, owner, confidentiality, and retention policy.
2. `DocumentParsed` extracts text, tables, diagrams, schemas, endpoint descriptions, and attachments.
3. `DocumentChunked` creates traceable chunks with stable IDs, offsets, headings, and source citations.
4. `SemanticAnalysisCompleted` identifies intent, domain, obligations, rules, and implementation impact.
5. `EntitiesExtracted` captures modules, services, endpoints, authentication, permissions, requirements, business rules, errors, rate limits, contracts, financial models, workflows, integrations, and dependencies.
6. `RelationshipsDetected` connects entities using typed edges such as `requires`, `implements`, `conflicts_with`, `supersedes`, `depends_on`, and `governed_by`.
7. `KnowledgeGraphUpdated` persists nodes and edges with confidence scores and provenance.
8. `VectorIndexUpdated` indexes chunks and generated summaries for semantic retrieval.
9. `BESKnowledgeRegistryUpdated` publishes canonical records, extracted requirements, and open questions.
10. `ArchitectureSuggestionsGenerated` compares the new knowledge against the current architecture and detects missing components.
11. `ImplementationTasksCreated` creates backlog items, acceptance criteria, dependencies, and risk notes.

## Supported Input Types

The ingestion layer must support PDF, DOCX, Markdown, Swagger, OpenAPI, DDEX, XML, JSON, CSV, YAML, SDK repositories, GitHub repositories, REST API documentation, GraphQL schemas, HTML, wiki pages, legal agreements, partner requirements, and technical documentation.

## New Information Handling

When a parser or agent detects information not already represented in BES, the system must create a BES knowledge record, connect it to existing entities, generate a specification draft, suggest a module or connector if needed, update the knowledge graph, update documentation, and notify the architecture layer.

## Platform Discovery

For each platform, the Platform Discovery Agent must discover and normalize developer portal URLs, API references, SDKs, authentication methods, OAuth scopes, DDEX support, content feed formats, documentation, submission process, partner programs, enterprise programs, content policies, financial model, technical restrictions, and legal requirements.

## Connector Generation

When a new platform is approved for implementation, BES generates a connector package with DTOs, models, routes, services, tests, documentation, credentials policy, retry policy, rate-limit policy, observability events, and manual approval checkpoints for external submissions.

## Required API Routes

| Route               | Capability                                                                                             |
| ------------------- | ------------------------------------------------------------------------------------------------------ |
| `/api/distribution` | Create and monitor delivery jobs, retries, receipts, and takedowns.                                    |
| `/api/releases`     | Manage releases, tracks, lifecycle state, approvals, and audit events.                                 |
| `/api/platforms`    | Register platforms, requirements, authentication profiles, and connector status.                       |
| `/api/analytics`    | Query streams, revenue, audience, growth, forecasts, and AI recommendations.                           |
| `/api/contracts`    | Analyze partner terms, generate drafts, manage human review checklists.                                |
| `/api/royalties`    | Configure splits, calculate statements, export payout and audit data.                                  |
| `/api/connectors`   | Generate, configure, test, and monitor platform connectors.                                            |
| `/api/metadata`     | Normalize, validate, enrich, and transform release metadata.                                           |
| `/api/rights`       | Manage ownership, licensing, territories, conflicts, and clearances.                                   |
| `/api/finance`      | Model subscription, commission, revenue-share, enterprise, marketplace, custom, and hybrid agreements. |
| `/api/partners`     | Manage partner onboarding, requirements, contacts, documents, and integration status.                  |
| `/api/knowledge`    | Ingest documents, retrieve knowledge records, update graph edges, and create architecture suggestions. |

## Data Model Skeleton

| Entity                   | Key Fields                                                                                           |
| ------------------------ | ---------------------------------------------------------------------------------------------------- |
| `Artist`                 | `id`, `legalName`, `displayName`, `country`, `taxProfileId`, `verificationStatus`                    |
| `Release`                | `id`, `artistId`, `title`, `version`, `upc`, `status`, `releaseDate`, `territories`, `approvalState` |
| `Track`                  | `id`, `releaseId`, `title`, `isrc`, `audioAssetId`, `explicit`, `contributors`, `rightsId`           |
| `Asset`                  | `id`, `type`, `uri`, `checksum`, `technicalMetadata`, `validationStatus`                             |
| `RightsClaim`            | `id`, `assetId`, `claimantId`, `territories`, `share`, `startDate`, `endDate`, `evidenceUri`         |
| `DistributionJob`        | `id`, `releaseId`, `platformId`, `status`, `attempts`, `lastError`, `receiptId`                      |
| `Platform`               | `id`, `name`, `developerPortal`, `authType`, `submissionModes`, `rateLimits`, `policies`             |
| `Connector`              | `id`, `platformId`, `version`, `capabilities`, `healthStatus`, `credentialsRef`                      |
| `AnalyticsFact`          | `id`, `platformId`, `releaseId`, `trackId`, `metric`, `country`, `period`, `value`                   |
| `RoyaltyStatement`       | `id`, `period`, `payeeId`, `gross`, `deductions`, `net`, `currency`, `status`                        |
| `Contract`               | `id`, `partnerId`, `type`, `status`, `riskScore`, `approvalRequired`, `effectiveDate`                |
| `KnowledgeRecord`        | `id`, `sourceId`, `entityType`, `canonicalName`, `summary`, `confidence`, `provenance`               |
| `ArchitectureSuggestion` | `id`, `knowledgeRecordId`, `suggestedChange`, `impact`, `dependencies`, `status`                     |
| `ImplementationTask`     | `id`, `suggestionId`, `title`, `acceptanceCriteria`, `dependencies`, `priority`, `status`            |

## Events and Workers

| Event                     | Worker                            | Responsibility                                                      |
| ------------------------- | --------------------------------- | ------------------------------------------------------------------- |
| `DocumentUploaded`        | `knowledge-ingestion-worker`      | Parse, chunk, classify, extract, and index documents.               |
| `NewKnowledgeDetected`    | `architecture-suggestion-worker`  | Generate specs, missing-module proposals, and implementation tasks. |
| `ReleaseSubmitted`        | `release-orchestration-worker`    | Validate metadata, rights, audio, artwork, and approvals.           |
| `ValidationFailed`        | `remediation-worker`              | Create actionable remediation tasks for artists or administrators.  |
| `DistributionRequested`   | `distribution-worker`             | Package and submit approved releases through connectors.            |
| `PlatformReceiptReceived` | `connector-reconciliation-worker` | Reconcile external delivery state with internal jobs.               |
| `AnalyticsImported`       | `analytics-normalization-worker`  | Normalize platform metrics and update dashboards.                   |
| `RevenueImported`         | `royalty-calculation-worker`      | Calculate royalties and generate statements.                        |
| `ContractDraftRequested`  | `contract-drafting-worker`        | Generate contract draft and legal review checklist.                 |

## Human Approval Gates

BES must not submit documents to external organizations, accept legal obligations, execute contracts, change financial agreements, or submit live releases to platforms without explicit user approval. Generated drafts, offers, platform applications, and legal checklists remain internal until approved.

## Risk Controls

- Legal risk: external terms and generated drafts require human review before acceptance or dispatch.
- Financial risk: royalty calculations require auditable inputs, immutable calculation versions, and statement approvals.
- Compliance risk: source documents require provenance, retention, confidentiality labels, and access controls.
- Platform risk: connectors require sandbox testing, rate-limit enforcement, idempotency keys, and delivery receipt reconciliation.
- Data risk: artist tax data, contract data, credentials, and payout details require encryption, least privilege, and audit logging.

## Implementation Plan

1. Create the BES Knowledge Registry schema and document ingestion interfaces.
2. Implement parsers and chunkers for the supported document types, starting with Markdown, OpenAPI, JSON, XML, CSV, and PDF.
3. Add entity extraction and relationship detection services with provenance-aware confidence scores.
4. Implement the Release, Metadata, Rights, Validation, Distribution, Analytics, Royalty, Contract, Partner, and Administration domain services.
5. Add platform connector scaffolding and generation templates.
6. Add route controllers and DTOs for the required API surface.
7. Add workers, events, queues, retry policies, and observability dashboards.
8. Add human approval workflows for legal, financial, external-submission, and live-distribution actions.
9. Add automated documentation generation from knowledge records, connectors, and OpenAPI definitions.
10. Add acceptance tests for ingestion, release submission, connector generation, royalty calculations, and contract approval gates.

## Task Queue Seed

| Priority | Task                                                                                                                                                   | Dependency                |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------- |
| P0       | Define `KnowledgeRecord`, `ArchitectureSuggestion`, and `ImplementationTask` persistence schemas.                                                      | None                      |
| P0       | Define human approval gate service and audit event model.                                                                                              | None                      |
| P1       | Implement document ingestion API and parser interfaces.                                                                                                | Knowledge schema          |
| P1       | Implement release lifecycle API and validation orchestration.                                                                                          | Approval gate service     |
| P1       | Implement platform registry and connector manifest schema.                                                                                             | Knowledge schema          |
| P2       | Implement contract-risk comparison service and draft-generation workflow.                                                                              | Approval gate service     |
| P2       | Implement royalty calculation models for subscription, commission, revenue share, enterprise, white label, marketplace, custom, and hybrid agreements. | Finance schema            |
| P2       | Implement analytics normalization and forecast pipelines.                                                                                              | Platform registry         |
| P3       | Generate first connector from discovered platform requirements.                                                                                        | Connector manifest schema |
