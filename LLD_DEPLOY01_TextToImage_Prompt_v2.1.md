# LLD-DGM-DEPLOY-01 — Text-to-Image Generation Prompt
## SmartRetail — Deployment Architecture (Production — us-east-1) — v2.1 Corrected

**Purpose:** Feed directly into a text-to-image model
**Corrections vs current diagram and draw.io prompt:**
  (1) Campaign Management System added to External section with arrow to API Gateway
  (2) API Gateway label updated to include system events as 4th stage
  (3) draw.io prompt CloudFront corrected — Single Distribution with path-based behaviors,
      NOT four separate CF distribution boxes (rendered image was correct; prompt was wrong)
**Everything else in the rendered image is correct and preserved.**

---

## Primary Prompt

```
Professional AWS deployment architecture diagram titled
"SmartRetail — Deployment Architecture (Production — us-east-1)".
Subtitle: "ECS Fargate · Three-Tier VPC · Multi-AZ · API Gateway VPC Link · Firehose Dual Delivery"
White background. Clean technical whitepaper illustration style.
AWS 2024 service icons. Sharp legible text. No gradients. No shadows.
High resolution 1600x1000 landscape.

OVERALL STRUCTURE: Three horizontal bands left to right:
  LEFT BAND (~18% width): External Users and External Sources.
  CENTER-LEFT BAND (~15% width): Edge & Global layer (CloudFront, API Gateway, Firehose, S3).
  CENTER-RIGHT LARGE BOX (~50% width): AWS Account containing VPC — the dominant element.
  RIGHT BAND (~17% width): External Platforms and CI/CD.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEFT BAND — EXTERNAL USERS AND SOURCES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label: "EXTERNAL USERS" in bold at top.

Four person silhouette icons stacked vertically, each with a label:
  "Store Manager"
  "SC Planner"
  "Executive"
  "Supplier Rep"
Black arrows from all four pointing right to Amazon CloudFront.

Below the person icons:
  Box: "POS Systems" — server/terminal icon.
  Black arrow pointing right to Store-Edge Aggregator below.

Store-Edge Aggregator box (dashed border, light purple fill):
  Label: "Store-Edge Aggregator"
  Sub-label: "(per DC/Store — outside AWS)"
  Blue arrow pointing right to Amazon Data Firehose.
  Arrow label: "HTTPS · PutRecordBatch · IAM SigV4"

[CORRECTION ADDED] Campaign Mgmt System box (dashed border, light green fill):
  Label: "Campaign Mgmt System"
  Sub-label: "(External — outside AWS)"
  Blue arrow pointing right to Amazon API Gateway.
  Arrow label: "HTTPS:443 · API key"
  Small annotation: "system events stage"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CENTER-LEFT BAND — EDGE & GLOBAL LAYER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Light pink/red fill labeled "EDGE (GLOBAL)". Contains:

COMPONENT 1 — Amazon CloudFront [ONE single distribution — NOT four separate boxes]:
  Large pink/salmon CloudFront circular AWS icon in a rounded box.
  Label: "Amazon CloudFront (Single Distribution)"
  WAFv2 badge in top-right corner.
  Inside box, small text listing path-based behaviors:
    "Behaviors (Path Patterns):"
    "/store-manager/* → S3: store-manager-mfe"
    "/sc-planner/*    → S3: sc-planner-mfe"
    "/supplier/*      → S3: supplier-mfe"
    "/executive/*     → S3: executive-mfe"
    "Default (*)      → S3: executive-mfe"
  "Origin Access Control (OAC)" label with lock icon.
  Four purple dashed arrows from CloudFront pointing to four S3 bucket icons:
    "store-manager-mfe (S3 Bucket)"
    "sc-planner-mfe (S3 Bucket)"
    "supplier-mfe (S3 Bucket)"
    "executive-mfe (S3 Bucket)"
  These are labeled "S3 ORIGINS (Private Buckets)".

COMPONENT 2 — Amazon API Gateway:
  Dark navy API Gateway circular AWS icon in a large rounded box.
  WAFv2 badge top-right. ACM badge top-right.
  Label: "Amazon API Gateway — Single Ingress"
  [CORRECTED] Sub-label multi-line:
    "Staff + Supplier + Firehose ingest"
    "+ System events (Campaign Mgmt)"
  Blue solid arrow arriving from CloudFront above. Arrow label: "HTTPS:443"
  Blue solid arrow arriving from Firehose (HTTP endpoint). Arrow label: "HTTP endpoint:443 · access key"
  [CORRECTED] Blue solid arrow arriving from Campaign Mgmt System in Left Band.
    Arrow label: "HTTPS:443 · API key"
  Blue solid arrow pointing RIGHT into the VPC. Arrow label: "VPC Link :8080"

COMPONENT 3 — Amazon Data Firehose (managed service):
  Purple Firehose circular AWS icon in a purple-bordered rounded box.
  Label: "Amazon Data Firehose (managed service)"
  Blue arrow arriving from Store-Edge Aggregator (from Left Band).
    Arrow label: "HTTPS · PutRecordBatch · IAM SigV4"
  TWO arrows leaving Firehose:
  Arrow A: Blue solid arrow going UP-RIGHT to Amazon API Gateway.
    Arrow label: "HTTP endpoint:443 (access key)"
  Arrow B: Orange dashed arrow going DOWN to S3 Raw Events Bucket.
    Arrow label: "native delivery · SSE-KMS"

COMPONENT 4 — Amazon S3 Raw Events Bucket:
  Green S3 bucket icon with lock.
  Label: "Amazon S3 Raw Events Bucket (SSE-KMS)"

COMPONENT 5 — Amazon S3 ML Artefacts Bucket:
  Green S3 bucket icon with lock.
  Label: "Amazon S3 ML Artefacts Bucket (SSE-KMS)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
AWS ACCOUNT BOX — VPC AND SUBNETS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Outer box: light blue fill. Solid blue border. Label: "AWS Account — us-east-1 (Production)"

Inside: Large VPC box. White fill. Darker blue border.
Label: "VPC / 10.0.0.0/16 · us-east-1"
VPC spans three AZ columns (AZ-a | AZ-b | AZ-c) across three horizontal tiers.

─── TIER 1: PUBLIC SUBNETS ───
Light indigo fill. Label: "PUBLIC SUBNETS" per AZ column.
Internet Gateway icon on the far left of the VPC.
Each AZ column has a NAT Gateway icon: "NAT Gateway"
Dashed arrows from NAT Gateways pointing right indicating outbound egress.

─── TIER 2: PRIVATE APP SUBNETS ───
Light green fill. Label: "PRIVATE APP SUBNETS"

THREE AZ COLUMNS. Each column contains:
  Orange-bordered box: "Amazon ECS Fargate Tasks (7 services)"
  Sub-label: "awsvpc mode (No public IPs)"
  Row of 7 small orange ECS Fargate task icons inside.
  Blue VPC Link arrow arriving from API Gateway into the leftmost AZ-a column.
  Arrow label: "VPC Link :8080"

SPANNING BELOW THE ECS COLUMNS:
  Two RDS Proxy boxes (in AZ-a and AZ-b):
    Blue RDS Proxy circular AWS icon.
    Label: "RDS Proxy"
    Sub-label: "connection pooling · :5432"
    Orange arrows from ECS tasks above pointing down to each RDS Proxy.
    Arrow label: "TCP:5432"

SPANNING FULL WIDTH (below RDS Proxy, within Private App Subnets):
  Pink EventBridge + purple SQS icons side by side.
  Label: "Amazon EventBridge + Amazon SQS"
  Sub-label: "(domain events · fan-out)"
  Dashed blue arrows from ECS tasks downward.

─── TIER 3: PRIVATE DATA SUBNETS ───
Light amber fill. Label: "PRIVATE DATA SUBNETS"

LEFT: RDS Multi-AZ section:
  Two database icons side by side:
    "Amazon RDS Primary (AZ-a)" — blue database icon with "P" badge
    Sub-label: "PostgreSQL · PITR · 7-day backup"
    "Amazon RDS Standby (AZ-b)" — blue database icon with "S" badge
    Sub-label: "PostgreSQL · PITR · 7-day backup"
  Double-headed orange dashed arrow between them.
  Label: "Sync Replication · auto-failover <60s"
  Orange arrows arriving from RDS Proxy above.

BOTTOM STRIP spanning full width — VPC ENDPOINTS:
  Label: "VPC ENDPOINTS (Interface unless noted)"
  Row of 10 AWS service icons with labels below each:
  S3 (Gateway) · Amazon SQS · Amazon EventBridge · Amazon Data Firehose ·
  AWS KMS · AWS ACM · AWS Secrets Manager · Amazon ECR · AWS Systems Manager · Amazon SageMaker
  Blue HTTPS:443 arrows arriving from ECS tasks and EventBridge above.
  Arrow label: "HTTPS:443"

─── ML PLATFORM (inside VPC, private subnets, right side) ───
Light purple fill. Label: "ML PLATFORM (Private Subnets)"
Two boxes stacked:
  "Amazon SageMaker Training Job" — teal SageMaker icon
  "Amazon SageMaker Batch Transform" — teal SageMaker icon
Blue arrows arriving from ECS tasks (Training / Transform jobs).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RIGHT BAND — EXTERNAL PLATFORMS AND CI/CD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label: "Outbound Egress HTTPS:443" with dashed arrow from NAT Gateways arriving here.
Four boxes stacked:

Box 1: "Log Management Platform"
  Sub-label: "(CloudWatch Logs / SIEM / etc.)"
  Logs/search icon.

Box 2: "Amazon ECR Container Registry + AWS CodePipeline + AWS CodeBuild"
  Two icons. Label includes:
  "14 pipelines · CDK deploy"
  "immutable tags"

Box 3: "APM Platform (Optional)"
  Sub-label: "(Datadog / New Relic / etc.)"
  Bar chart icon. Dashed border (conditional).

Box 4: "Alerting Platform"
  Sub-label: "(PagerDuty / Slack / Email / SMS)"
  Bell icon. Red border.
  Dashed purple arrow arriving from NAT Gateways labeled "Outbound Egress HTTPS:443"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEGEND (bottom strip, full width)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
White fill, grey border. Six entries in a horizontal row:
  ─────►  API / VPC Link Traffic (:8080) — solid blue (thick)
  ─────►  Database Traffic (TCP:5432) — solid orange
  - - - ►  Private HTTPS Traffic (HTTPS:443 via VPC Endpoints) — blue dashed
  - - - ►  Outbound Egress Traffic (HTTPS:443) — dark grey dashed
  ─────►  Public Internet Traffic (HTTPS:443) — solid black
  · · · ►  Firehose Native Delivery (SSE-KMS) — orange dashed
  □       Subnet boundary icon
  ⊙       Gateway / Endpoint icon
```

---

## Critical Clarifications for Image Generation Models

```
CRITICAL 1 — ONE CLOUDFRONT DISTRIBUTION:
Amazon CloudFront must be shown as ONE Single Distribution with path-based
behaviors routing to four S3 origin buckets. Do NOT show four separate
CloudFront distribution boxes. The single distribution is labeled
"Amazon CloudFront (Single Distribution)" and lists the path patterns inside.

CRITICAL 2 — CAMPAIGN MGMT:
Campaign Management System must appear in the External left band with an
HTTPS:443 arrow pointing to Amazon API Gateway. No arrow to EventBridge.
The system events stage of API Gateway handles this connection.

CRITICAL 3 — FIREHOSE DIRECTION:
Amazon Data Firehose sends to API Gateway (HTTP endpoint · access key).
API Gateway does NOT send to Firehose.
Store-Edge Aggregator sends to Firehose (PutRecordBatch · IAM SigV4).
```

---

## Style Modifiers

### For Midjourney
```
--style raw --ar 16:10 --v 6 --quality 2
professional AWS production deployment architecture diagram, three-tier VPC
subnet diagram, enterprise infrastructure whitepaper style, white background,
AZ columns, color-coded subnet tiers, sharp text labels, labeled arrows with
port numbers, no artistic effects, no shadows, no gradients
```

### For DALL-E / GPT-4o
```
Render this as a precise production AWS deployment architecture diagram.
One large VPC box with three horizontal tiers across three AZ columns.
External components on the left: person icons, Store-Edge Aggregator, and
Campaign Management System (separate box with arrow to API Gateway).
ONE Amazon CloudFront distribution with path-based behaviors — not four boxes.
API Gateway label includes four stages including system events.
Two RDS nodes only (Primary AZ-a + Standby AZ-b). White background.
All port labels and arrow labels visible.
```

### Universal negative prompt
```
no four separate CloudFront distributions, no Campaign Mgmt arrow to EventBridge,
no direct CMS to EventBridge, no three RDS standby nodes, no Kinesis Data Streams,
no DynamoDB, no ALB, no ElastiCache, no dark background, no shadows, no gradients
```

---

## Corrections Summary

| # | Element | Current State | Corrected State |
|---|---|---|---|
| 1 | Campaign Mgmt System | Absent from both rendered image and draw.io prompt | Added to External left band with HTTPS:443 arrow to API Gateway, "system events stage" label |
| 2 | API Gateway label | "Staff + Supplier + Firehose ingest" | "Staff + Supplier + Firehose ingest + System events (Campaign Mgmt)" |
| 3 | CloudFront (draw.io prompt only) | "CLOUDFRONT × 4 — 4 small CF distribution boxes" | One Single Distribution with path-based behaviors. Rendered image was already correct. |
