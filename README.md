# Treatment & Records Follow-up Agent — Implementation

## Purpose
Implementation plan for building the Treatment & Records Follow-up Agent in a blank Salesforce SDO/Developer org for a personal-injury law firm.

## Build Order
1. Create the Salesforce data foundation.
2. Add deterministic follow-up rules using Custom Metadata.
3. Implement Scheduled Apex + Batch Apex evaluation.
4. Add duplicate prevention and stalled-matter handling.
5. Build the Case Manager work queue.
6. Enforce server-side human approval and security.
7. Add communication handling (email + Tasks/manual channels initially).
8. Add inbound response classification.
9. Add reports and dashboards.
10. Configure Prompt Builder.
11. Configure the Agentforce employee agent.
12. Complete testing, UAT, and demo validation.

## Core Data Model
- Matter__c
- Medical_Provider__c
- Treatment_Event__c
- Records_Request__c
- Follow_Up_Recommendation__c

### Matter__c
Client, Status, Practice Area, Sign Up Date, Demand Sent Date, Stalled, Stalled Since.

### Medical_Provider__c
Provider Name, Email, Phone, Fax, Provider Type, Active.

### Treatment_Event__c
Matter, Provider, Treatment Date, Treatment Status, Notes.

### Records_Request__c
Matter, Provider, Request Date, Received Date, Status, Request Type.

### Follow_Up_Recommendation__c
Matter, Recommendation Type, Status, Priority, Audience, Channel, Reason, Gap Days, Request Age Days, Follow-up Count, Draft Message, Draft Source, Rationale, Approval fields, Sent Date, Response fields, Classification, Active Key.

## Important Business Rule
Records_Request__c represents an actual medical-record request that has already been made. Follow_Up_Recommendation__c represents a recommended follow-up action for an existing business event. The AI/Agentforce layer must not invent records requests.

## Automation Architecture
Scheduled Apex → Batch Apex → Matter Selector → Matter Context → Deterministic Rule Engine → Evaluation Service → Follow_Up_Recommendation__c → Case Manager Work Queue → Human Approval → Send/Task/Manual Channel → Inbound Response Classification → Next Deterministic Action

## Custom Metadata
- Follow_Up_Rule__mdt: configurable thresholds and rule definitions.
- Follow_Up_Setting__mdt: global settings/feature flags.
- Response_Classification_Rule__mdt: deterministic response classification rules.

Example thresholds:
- Treatment gap: 21 days
- Records follow-up: 14 days
- Repeat records follow-up: 10 days
- Provider escalation: after 2 follow-ups + 7 days
- Missing bills: 30 days

## Recommended Apex Components
- FollowUpNightlyScheduler
- FollowUpEvaluationBatch
- FollowUpMatterSelector
- FollowUpMatterContext
- FollowUpRuleEngine
- FollowUpEvaluationService

Design for bulkification, governor-limit safety, CRUD/FLS, sharing/record access, namespace/package safety, deterministic tests, and duplicate prevention.

## Work Queue
LWC: treatmentRecordsFollowUpConsole

Case manager capabilities:
Review recommendation, inspect context, edit draft, approve, reject, override, send, and log response.

Human approval must be enforced server-side.

## Agentforce Scope
Internal employee assistant with three topics:
1. Matter Follow-up Review
2. Follow-up Drafting
3. Inbound Response Handling

Guardrails:
- No legal decision-making.
- AI does not decide whether follow-up is required.
- No external send without required human approval.
- Respect Salesforce security and permissions.

## Prompt Builder
Recommended templates:
- Client Follow-up Draft
- Provider Follow-up Draft
- Stalled Matter Summary
- Inbound Response Classification

## Current Status
Analysis and implementation planning is complete enough to begin the SDO build. The next step is the Salesforce data foundation and sample data, followed by the deterministic rule engine.
