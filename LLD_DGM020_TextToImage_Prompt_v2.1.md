# LLD-DGM-020 — Text-to-Image Generation Prompt
## Network & Port Topology — v2.1 Corrected

**Purpose:** Feed directly into a text-to-image model
**Corrections vs current diagram:**
  (1) Campaign Management System added to External section with HTTPS:443 arrow to API Gateway
  (2) Campaign Mgmt draw.io prompt arrow corrected: was EventBridge, must be API Gateway
  (3) Store-Edge Aggregator arrow label clarified to include IAM SigV4
**Everything else is correct and preserved.**

---

## Primary Prompt

```
Professional AWS network and port topology diagram titled
"LLD-DGM-020 — Network & Port Topology".
White background. Clean technical network architecture style.
No gradients. No shadows. Arial or Helvetica font. Sharp legible text.
AWS 2024 service icons. Port numbers and protocol labels on every arrow.
High resolution 1400x900 landscape.

OVERALL LAYOUT:
  LEFT COLUMN (~22% width): External components — outside VPC boundary.
  CENTER-RIGHT LARGE BOX (~65% width): VPC (10.0.0.0/16) — three stacked tiers.
  RIGHT COLUMN (~13% width): External Platforms.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEFT COLUMN — EXTERNAL (outside VPC)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label: "EXTERNAL" — bold purple, top of column.
Light purple fill, dashed purple border.
Five stacked elements top to bottom:

Element 1: "Internet Users / MFE browsers"
  Person silhouette group icon.
  Black arrow pointing right to Amazon CloudFront.
  Arrow label: "HTTPS:443"

Element 2: "Amazon CloudFront"
  Pink/salmon CloudFront circular AWS icon in a rounded box.
  Black arrow pointing right to Amazon API Gateway.
  Arrow label: "HTTPS:443"

Element 3: "Amazon API Gateway (Edge-optimized)"
  Dark navy API Gateway circular AWS icon in a rounded box.
  Blue arrow pointing RIGHT into the VPC Tier 2.
  Arrow label: "HTTPS:443 → container port 8080 (VPC Link)"
  Small annotation: "From APIGW SG only"
  This is the VPC Link entry into the ECS cluster.

Element 4: "Store-Edge Aggregator"
  Dashed-border box. Store/building icon.
  Blue arrow pointing RIGHT to Amazon Data Firehose.
  Arrow label: "PutRecordBatch · HTTPS:443 · IAM SigV4"

Element 5 [CORRECTION ADDED]: "Campaign Mgmt System"
  Dashed-border box. Megaphone/broadcast icon.
  Blue arrow pointing RIGHT to Amazon API Gateway (Element 3 above).
  Arrow label: "HTTPS:443 · API key"
  Small annotation near arrow: "system events stage"
  DO NOT draw an arrow from Campaign Mgmt to EventBridge directly.

BETWEEN LEFT COLUMN AND VPC (floating, external managed service):
"Amazon Data Firehose (Managed Service)"
  Purple Firehose circular AWS icon in a purple-bordered box.
  TWO arrows leaving Firehose:
  Arrow A: Blue solid arrow pointing RIGHT to Amazon API Gateway.
    Arrow label: "HTTPS:443 (HTTP endpoint)"
  Arrow B: Orange dashed arrow pointing DOWN to S3 (native delivery, shown
    as a small annotation — S3 archive is outside VPC).
    Arrow label: "native delivery"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MAIN VPC BOX (center-right)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Large rounded rectangle. Light teal/blue fill. Solid green border 2px.
Label top-left: AWS cloud icon + "VPC (10.0.0.0/16)"

THREE HORIZONTAL TIERS stacked top to bottom inside the VPC:

─── TIER 1 — PUBLIC SUBNETS ───
Light blue fill. Label: "TIER 1 — Public Subnets"
One Internet Gateway icon on the left edge of the VPC.
Three AZ columns (AZ-a · AZ-b · AZ-c), each containing one NAT Gateway:
  Navy circular NAT Gateway icon. Labels: "NAT Gateway" with AZ label above.
  Dashed arrows from NAT Gateways pointing UP-RIGHT indicating outbound egress.

─── TIER 2 — PRIVATE APP SUBNETS ───
Light green fill. Label: "TIER 2 — Private App Subnets (No public IPs)"

LEFT PORTION — "ECS Fargate Tasks (awsvpc mode) — No public IPs":
  Light dashed border inside Tier 2.
  Seven orange ECS task icons in a row with labels below each:
    IMS · DFS · RE · SUP · PPS · ARS · SIS
    Each labeled: "(min 2 tasks across AZs)"
  Sub-label below the row: "Inbound: API Gateway VPC Link (port 443/8080)"
    "from APIGW SG only · No public IPs"
  Blue arrow arriving from the left (from API Gateway in External section).
  Arrow label: "HTTPS:443 → container port 8080 (VPC Link)"
  Note: "From APIGW SG only"

RIGHT PORTION — "RDS Proxy":
  Blue RDS Proxy circular AWS icon in a blue-bordered box on the right side of Tier 2.
  Label: "RDS Proxy"
  Sub-label: "Inbound: ECS task SGs only (port 5432)"
  Orange arrows from the ECS task row pointing right to RDS Proxy.
  Arrow label: "TCP:5432"

BOTTOM OF TIER 2 — "Amazon EventBridge + Amazon SQS":
  Pink/magenta EventBridge icon + purple SQS icon side by side.
  Label: "Amazon EventBridge + Amazon SQS"
  Sub-label: "(Managed Services) · Accessed via VPC Endpoints"
  Blue dashed arrows from ECS tasks pointing down to this section.
  Blue HTTPS:443 arrow from this section pointing DOWN to Tier 3 VPC Endpoints.
  Arrow label: "HTTPS:443 (via VPC Endpoints)"

─── TIER 3 — PRIVATE DATA SUBNETS ───
Light amber fill, amber dashed border.
Label: "TIER 3 — Private Data Subnets"
Three sub-sections side by side:

Sub-section A — RDS (left, large):
  Blue-bordered box with RDS PostgreSQL circular AWS icon.
  Label: "Amazon RDS PostgreSQL Multi-AZ"
  Sub-label: "(port 5432, not publicly accessible)"
  Inside: TWO database cylinder icons side by side:
    Left cylinder labeled "Primary (AZ-a)" — blue P icon
    Right cylinder labeled "Standby (AZ-b)" — blue S icon
  Orange dashed double-headed arrow between cylinders.
  Label: "Sync replication · Auto-failover < 60s"
  Orange arrow arriving from RDS Proxy above. Arrow label: "TCP:5432"

Sub-section B — S3 Gateway (center):
  Green S3 bucket icon.
  Label: "S3 Gateway VPC Endpoint"
  Blue arrow arriving from above labeled "HTTPS:443"

Sub-section C — Interface VPC Endpoints (right, wide):
  Label: "Interface VPC Endpoints (port 443 for all)"
  Three rows of circular AWS service icons with labels below each:
  Row 1: SQS · EventBridge · Firehose · KMS · ACM
  Row 2: Secrets Manager · ECR · SSM · SageMaker
  Blue HTTPS:443 arrows arriving from ECS tasks and EventBridge above.
  Arrow label: "HTTPS:443 (via Interface Endpoints)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RIGHT COLUMN — EXTERNAL PLATFORMS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label: "EXTERNAL PLATFORMS" — bold purple, top of column.
Light purple fill, dashed purple border.
One box:
  "SaaS / Third-party APIs"
  Circular interconnected nodes icon.
  Dashed grey double-headed arrow connecting to the NAT Gateways in Tier 1.
  Arrow label: "HTTPS:443 (outbound only)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEGEND (bottom strip, full width)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
White fill, grey border. Six entries in a horizontal row:
  ─────►  Inbound via VPC Link (From API Gateway SG only) — solid blue (thick)
  ─────►  Private connectivity (via VPC / Endpoints) — solid blue (thin)
  ─────►  Database connection (TCP:5432) — solid orange
  - - - ►  Outbound via NAT Gateway (HTTPS:443) — dark grey dashed
  □ - -    Availability Zone — dashed light blue rectangle
  SG       Security Group reference

Right of legend: small note box:
  "All services in private subnets have no public IPs (awsvpc mode)."
```

---

## Critical Clarification for Image Generation Models

```
CRITICAL — CAMPAIGN MGMT PLACEMENT AND ARROW:
Campaign Management System must appear in the EXTERNAL left column below
the Store-Edge Aggregator. Its arrow points RIGHT to Amazon API Gateway.
Arrow label: "HTTPS:443 · API key"
DO NOT draw any arrow from Campaign Mgmt to Amazon EventBridge or SQS.
In a network topology diagram, Campaign Mgmt connects to API Gateway only.
API Gateway internally routes to EventBridge via AWS service integration
using the EventBridge VPC endpoint already shown in Tier 3.

CRITICAL — FIREHOSE DIRECTION:
Amazon Data Firehose is shown OUTSIDE the VPC, as a floating managed service.
One arrow from Firehose goes TO Amazon API Gateway (HTTPS:443, HTTP endpoint delivery).
One arrow from Firehose goes to S3 (native delivery).
The Store-Edge Aggregator arrow goes TO Firehose (PutRecordBatch, IAM SigV4).
API Gateway does NOT send traffic to Firehose.
```

---

## Style Modifiers

### For Midjourney
```
--style raw --ar 3:2 --v 6 --quality 2
professional AWS network topology diagram, VPC subnet diagram, port numbers
on every arrow, three horizontal tiers inside VPC, technical infrastructure
diagram, whitepaper style, white background, no artistic effects,
no shadows, no gradients, sharp text labels
```

### For DALL-E / GPT-4o
```
Render this as a precise technical network topology diagram. White background.
One large VPC box with three horizontal tiers (Public Subnets, Private App
Subnets, Private Data Subnets). External components on the left side outside
the VPC boundary. Campaign Management System in the external left column with
an HTTPS:443 arrow to API Gateway only — no arrow to EventBridge. All port
numbers visible on every connection arrow. Two RDS cylinders only (Primary + Standby).
Nine VPC endpoint icons in the Tier 3 right section including Secrets Manager.
```

### Universal negative prompt
```
no Campaign Management arrow to EventBridge, no direct CMS to EventBridge,
no Kinesis Data Streams, no DynamoDB VPC endpoint, no ALB, no ElastiCache,
no third RDS standby cylinder, no 3D effects, no shadows, no gradients,
no dark background, no missing port labels
```

---

## Corrections vs Current Diagram

| # | Element | Current (wrong / missing) | Corrected |
|---|---|---|---|
| 1 | Campaign Mgmt System | Absent from rendered diagram; draw.io prompt has wrong arrow to EventBridge | Added to External section with HTTPS:443 arrow to API Gateway labeled "system events stage" |
| 2 | Store-Edge arrow label | "PutRecordBatch · HTTPS:443" | "PutRecordBatch · HTTPS:443 · IAM SigV4" — clarifies credential mechanism |
| 3 | draw.io prompt | `Campaign Mgmt System → EventBridge (managed)` | Replace with `Campaign Mgmt System → API Gateway: HTTPS:443 · API key (system events stage)` |
