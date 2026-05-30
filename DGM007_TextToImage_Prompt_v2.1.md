# DGM-007 — Text-to-Image Generation Prompt
## Smart Retail Platform — Deployment Architecture (us-east-1) — v2.1 Corrected

**Purpose:** Feed directly into a text-to-image model
**Corrections vs current diagram:**
  (1) Campaign Management System added to External section with arrow to API Gateway
  (2) Secrets Manager added to VPC Interface Endpoints strip
**Everything else in the rendered image is correct — preserved as-is.**

---

## Primary Prompt

```
Professional AWS deployment architecture diagram titled
"Smart Retail Platform — Deployment Architecture (us-east-1)".
White background. Clean technical whitepaper illustration style.
No gradients. No shadows. Arial or Helvetica font throughout.
Sharp text. AWS 2024 service icons — colored circular icons with white symbols.
High resolution 1600x1000 landscape.

OVERALL LAYOUT: Two main columns.
  Left column (~25% width): External components — outside the VPC boundary.
  Right column (~75% width): Main VPC box plus external platforms far right.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEFT COLUMN — EXTERNAL COMPONENTS (outside VPC)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Stacked top to bottom in the left column:

BLOCK 1 — "EXTERNAL" section (light green fill, dashed green border):
  Two actor boxes inside:
  Box A: Person silhouette group icon.
    Label: "Internet Users"
    Black arrow pointing right labeled "HTTPS/443" going to CloudFront.
  Box B: Shopping cart + POS terminal icon.
    Label: "POS Systems"
    (Visual only — connects to Store-Edge Aggregator below)

BLOCK 2 — Amazon CloudFront (medium-sized rounded box, pink/red fill):
  Pink/salmon CloudFront circular AWS icon.
  Label: "Amazon CloudFront"
  Sub-label: "Serves 4 MFE Distributions"
  Small red WAFv2 badge in top-right corner.
  Black arrow pointing right labeled "HTTPS/443" going to Amazon API Gateway.

BLOCK 3 — Amazon API Gateway (medium-sized rounded box, dark navy fill):
  Dark blue API Gateway circular AWS icon.
  Label: "Amazon API Gateway"
  Sub-label multi-line:
    "Single ingress for all APIs"
    "Staff · Supplier · Firehose ingest"
    "System events (Campaign Mgmt)"
  Small red WAFv2 badge in top-right corner.
  Blue arrow pointing right labeled "VPC Link :8080" going into the VPC.

BLOCK 4 — Store-Edge Aggregator (dashed-border box, light purple fill):
  Router/server icon.
  Label: "Store-Edge Aggregator"
  Sub-label: "(per DC/Store)"
  Blue arrow pointing RIGHT to Amazon Data Firehose below.
  Arrow label: "PutRecordBatch · SigV4"

BLOCK 5 — [CORRECTION ADDED] Campaign Mgmt System (dashed-border box, light green fill):
  Megaphone/broadcast icon.
  Label: "Campaign Mgmt System"
  Sub-label: "(External)"
  Blue arrow pointing RIGHT to the Amazon API Gateway box above.
  Arrow label: "POST /system/v1/events · API key"
  Small annotation near the arrow: "system events stage"
  This arrow must be visually distinct from the Firehose → API Gateway arrow.

BLOCK 6 — Amazon Data Firehose (purple-bordered rounded box):
  Purple Firehose circular AWS icon.
  Label: "Amazon Data Firehose"
  Sub-label: "(managed service)"
  TWO arrows from Firehose:
  Arrow A: Blue solid arrow pointing RIGHT labeled "HTTP endpoint · access key"
    going UP to Amazon API Gateway.
  Arrow B: Orange dashed arrow pointing DOWN labeled "native delivery"
    going to the S3 bucket below.

BLOCK 7 — S3 Bucket (green-bordered box, below Firehose):
  Green S3 bucket AWS icon with lock icon.
  Label: "S3 Bucket"
  Sub-label: "smartretail-raw-events/"
  Sub-label 2: "(Raw events archive)"
  Sub-label 3: "SSE-KMS"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MAIN VPC BOX (right ~65% of canvas)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Large rounded rectangle. Light blue fill. Solid blue border 2px.
Label top-left: small AWS cloud icon + "VPC — us-east-1"

INSIDE THE VPC — four horizontal sections stacked top to bottom:

─── SECTION 1: PUBLIC SUBNETS (thin strip at top) ───
Light indigo fill. Label: "Public Subnets (for egress)"
Four components in a row:
  Left: Internet Gateway navy circular icon, label "Internet Gateway"
  Three NAT Gateway navy icons with labels:
    "NAT Gateway AZ-a"
    "NAT Gateway AZ-b"
    "NAT Gateway AZ-c"
  Dashed purple arrow from rightmost NAT Gateway exiting the VPC right boundary
  pointing to External Platforms (far right). Label: "Egress (HTTPS)"

─── SECTION 2: PRIVATE APP SUBNETS (tall middle section) ───
Light green fill. Label top: "Private App Subnets (Application Tier)"
Sub-label: "ECS AZ-spread — all 7 services · awsvpc networking"

THREE AZ COLUMNS separated by vertical dashed lines (AZ-a | AZ-b | AZ-c):

Each AZ column contains:
  An orange-bordered box labeled "Amazon ECS (Fargate)"
  Inside: Two rows of orange ECS task icons with white text labels:
    Row 1: SIS · DFS · IMS · RE
    Row 2: SUP · PPS · ARS
  All 7 services must appear in every AZ column.
  Each task icon is a small orange hexagon or ECS icon with the service abbreviation.

RIGHT SIDE of Private App Subnets (separate from AZ columns):
  Blue-bordered box with RDS Proxy circular AWS icon.
  Label: "Amazon RDS Proxy"
  Sub-label: "connection pooling — all ECS services"
  Orange dashed arrow from ECS tasks pointing right to RDS Proxy.

BOTTOM OF PRIVATE APP SUBNETS — "Messaging (Managed)" section:
  Pink/magenta fill. Spans full width of Private App Subnets below the AZ columns.
  Label: "Messaging (Managed)"
  Two boxes side by side:
  Left box: Pink EventBridge circular AWS icon.
    Label: "Amazon EventBridge Custom Bus"
  Right box: Purple SQS circular AWS icon.
    Label: "Amazon SQS (Std + FIFO Queues)"
    Two queue icons: one Standard, one FIFO.
  Dashed blue arrows from ECS tasks downward to EventBridge.
  Dashed blue arrows from SQS leftward back to ECS tasks (consumer arrows).

─── SECTION 3: PRIVATE DATA SUBNETS (strip below Private App) ───
Light amber fill, amber dashed border. Label: "Private Data Subnets (Data Tier)"

THREE SUB-SECTIONS side by side:

Sub-section A — RDS (left, large):
  Blue-bordered box with RDS PostgreSQL circular AWS icon.
  Label: "Amazon RDS for PostgreSQL (Multi-AZ)"
  Inside: TWO database cylinder icons side by side:
    Left cylinder: "Primary (AZ-a)" — darker blue
    Right cylinder: "Standby (AZ-b)" — lighter blue
  ONLY TWO cylinders — do NOT show a third standby.
  Orange dashed double-headed arrow between the two cylinders.
  Label below arrow: "Synchronous replication · auto-failover <60s"
  Blue "PITR · 7-day backup" badge pill in top-right of the RDS box.
  Orange arrow from RDS Proxy above pointing down into Primary cylinder.

Sub-section B — S3 VPC Gateway Endpoint (center):
  Green S3 icon with VPC endpoint badge.
  Label: "S3 VPC Gateway Endpoint"

Sub-section C — VPC Interface Endpoints (right, wide):
  Label: "VPC Interface Endpoints (PrivateLink)"
  Row of nine small circular AWS service icons with labels below each.
  The nine endpoints in order:
    SQS · EventBridge · Firehose · KMS · Secrets Manager · ACM · ECR · SSM · SageMaker
  [CORRECTION: Secrets Manager must be included between KMS and ACM]

─── SECTION 4: ML PLATFORM (bottom section inside VPC) ───
Light purple fill, purple dashed border.
Label: "ML Platform (Private Subnets)"
Three boxes in a row:
  Box 1: Teal SageMaker icon. Label: "Amazon SageMaker Training Job"
  Box 2: Teal SageMaker icon. Label: "Amazon SageMaker Batch Transform"
  Box 3: Green S3 icon with lock. Label: "S3 ML Bucket (ML artefacts) SSE-KMS"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FAR RIGHT COLUMN — EXTERNAL PLATFORMS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label: "EXTERNAL PLATFORMS" (small caps, grey)
Three boxes stacked:
  Box 1: Analytics/logs icon. Label: "Log Management Platform"
    Sub-label: "(e.g., Splunk, Datadog Logs)"
  Box 2: Bar chart icon. Label: "APM Platform (Conditional)"
    Sub-label: "(e.g., Datadog, New Relic)"
  Box 3: Bell/alert icon. Red border. Label: "Alerting Platform"
    Sub-label: "(PagerDuty / Slack / Email / SMS)"
  Dashed purple arrows arriving from the VPC NAT Gateways (egress path).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
COMPLETE ARROW LIST
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Arrow 1: Internet Users → CloudFront
  Solid black arrow. Label: "HTTPS/443"

Arrow 2: CloudFront → API Gateway
  Solid black arrow. Label: "HTTPS/443"

Arrow 3: Store-Edge Aggregator → Amazon Data Firehose
  Solid blue arrow. Label: "PutRecordBatch · SigV4"
  This is a DIRECT connection. No API Gateway in between.

Arrow 4 [CORRECTION ADDED]: Campaign Mgmt System → Amazon API Gateway
  Solid blue arrow. Label: "POST /system/v1/events · API key"
  Annotation: "system events stage"

Arrow 5: Amazon Data Firehose → Amazon API Gateway
  Solid blue arrow going UP from Firehose to API Gateway.
  Label: "HTTP endpoint · access key"

Arrow 6: Amazon Data Firehose → S3 Bucket (raw events)
  Orange DASHED arrow going DOWN. Label: "native delivery"

Arrow 7: Amazon API Gateway → ECS Cluster (inside VPC)
  Solid blue arrow crossing VPC boundary. Label: "VPC Link :8080"

Arrow 8: ECS tasks → Amazon RDS Proxy
  Orange dashed arrows from ECS task groups to RDS Proxy. Label: "TCP:5432"

Arrow 9: Amazon RDS Proxy → RDS Primary cylinder
  Orange solid arrow. Label: "TCP:5432"

Arrow 10: ECS tasks → Amazon EventBridge (domain events)
  Dashed blue arrows downward from ECS to EventBridge. Label: "domain events"

Arrow 11: Amazon SQS → ECS tasks (consumers)
  Dashed blue arrows. Label: "SQS messages"

Arrow 12: NAT Gateways → External Platforms (egress)
  Dashed purple arrow exiting VPC right boundary. Label: "Egress (HTTPS)"

Arrow 13: ECS → VPC Interface Endpoints (implicit)
  Small dashed grey arrows. Label: "VPC Endpoints (PrivateLink)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEGEND AND NOTES (bottom strip)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Three boxes side by side at the bottom:

Box 1 — "LEGEND — LINE STYLES & COLORS":
  ─────►  HTTPS (User Traffic) — solid black
  ─────►  Service / API Invocation — solid blue
  ─────►  Database Traffic (TCP:5432) — solid orange
  - - -►  Private / Internal Connections — dashed blue
  · · ·►  Data Delivery (S3 Native) — orange dashed
  - - -►  Egress to Internet (HTTPS) — purple dashed

Box 2 — "AWS ICONS — KEY":
  Six icon + label pairs:
  Amazon CloudFront · Amazon API Gateway · Amazon Data Firehose
  Amazon ECS (Fargate) · Amazon RDS for PostgreSQL · Amazon RDS Proxy

Box 3 — "NOTES":
  Four bullet points:
  "· All ECS tasks run in private subnets with awsvpc network mode (No public IPs)"
  "· All traffic to AWS services uses VPC Endpoints (PrivateLink / Gateway)"
  "· NAT Gateways used for outbound internet access (patching, updates, egress)"
  "· Data at rest encrypted with KMS; In-transit TLS 1.2+ everywhere"
  "· Campaign Mgmt System calls API Gateway (system stage). API GW → EventBridge via AWS service integration."
```

---

## Style Modifiers

### For Midjourney
```
--style raw --ar 16:10 --v 6 --quality 2
professional AWS deployment architecture diagram, network topology diagram,
infrastructure whitepaper style, white background, clean corporate technical
illustration, sharp text labels, labeled arrows, color-coded VPC subnet zones,
no artistic effects, no shadows, no gradients
```

### For DALL-E / GPT-4o
```
Render this as a precise technical infrastructure diagram. White background.
One large VPC box dominating the right side. External components on the left.
Sharp text throughout. All arrows labeled. Color-coded subnet zones inside the VPC.
Three AZ columns each showing all 7 ECS services. RDS showing exactly 2 database
cylinders (Primary + Standby only). Nine VPC endpoint icons in a row including
Secrets Manager. Campaign Management System must appear as an external box with
an arrow to API Gateway.
```

### Universal negative prompt
```
no 3D effects, no shadows, no gradients, no photorealistic elements,
no dark background, no artistic interpretation, no missing VPC endpoints,
no Kinesis Data Streams, no DynamoDB, no ALB, no ElastiCache,
no third RDS standby node
```

---

## Corrections Summary vs Current Diagram

| # | Element | Current State | Corrected State |
|---|---|---|---|
| 1 | Campaign Mgmt System | Missing from External section | Added with blue arrow to API Gateway labeled "system events stage" |
| 2 | Secrets Manager VPC endpoint | Missing from Interface Endpoints | Added between KMS and ACM in the endpoints strip |
| 3 | API Gateway sub-label | "Single ingress for all APIs + Firehose HTTP endpoint" | "Single ingress: staff · supplier · Firehose ingest · system events" |

All other elements in the current diagram are correct and preserved.
