# DGM-003 — Text-to-Image Generation Prompt
## Smart Retail Platform — AWS Application Architecture (v2.1 Corrected)

**Purpose:** Feed directly into a text-to-image model (Midjourney, DALL-E, Firefly, Imagen, etc.)
**Corrections applied:** Campaign Mgmt via API GW · API GW → EventBridge service integration ·
RDS 2-node only · API GW 4-stage label · footnote corrected

---

## Primary Prompt

```
Professional AWS cloud architecture diagram titled "Smart Retail Platform — AWS Application
Architecture". Technical whitepaper illustration style. White background. Clean crisp edges.
No gradients. No shadows. Arial or Helvetica font throughout. All text sharp and legible.
Publication-quality technical diagram. Color-coded zones with thin bordered rectangular
sections. AWS 2024 icon set — colored circular service icons with white symbols inside.
High resolution 1600x1000 landscape.

FOUR VERTICAL ZONES LEFT TO RIGHT:

ZONE 1 — "External / Users" (leftmost, ~15% width)
Light green fill #F0FAF0, dashed green border.
Top section contains four person silhouette icons stacked vertically, each in a small
light-green rounded box: "Store Manager", "SC Planner", "Supplier Rep" (dashed border,
external), "Executive".
Below the persons: a dashed-border box labeled "POS / E-Commerce Platform" with a
shopping cart icon.
Below that: a solid-border box labeled "Store-Edge Aggregator (per DC/Store)" with a
router/server icon, note "(per DC/Store)" in small text below.
Bottom of zone: a dashed-border box labeled "Campaign Mgmt System" with a megaphone icon.

ZONE 2 — "Edge & Identity" (second from left, ~20% width)
Light red/pink fill #FFF0F0, solid red border, bold red label "Edge & Identity".
Top: Large rounded box for "Amazon CloudFront" with pink CloudFront circular AWS icon.
  Inside: bullet list in small text:
  "· Single Distribution for 4 MFEs"
  "· Path-based behaviors: /store-manager/*, /sc-planner/*, /supplier/*, /executive/*"
  "· WAFv2 + CachingOptimized policy"
  "· Custom Error Response (404→200 with index.html)"
  WAFv2 badge (small red pill) in top-right corner of box.
  OAC label on right edge with a downward arrow to the MFE buckets below.
Middle-left of Zone 2: "MFE Static Assets (Private S3 Buckets)" header above four small
  green S3 bucket icons in a row: "store-manager-mfe", "sc-planner-mfe",
  "supplier-mfe", "executive-mfe". Small lock icon on each. Label below:
  "Private S3 Buckets (Block Public Access = ON)". OAC arrow connects CloudFront to these.
Center of Zone 2: Large dark-navy rounded box for "Amazon API Gateway" with dark-blue
  API Gateway AWS icon.
  WAFv2 badge top-right corner.
  Label inside: "Single Ingress"
  Sub-label in smaller text: "(staff + supplier + Firehose ingest + system events)"
  Second sub-label: "4 stages — VPC Link to ECS · AWS svc integration to EventBridge"
Bottom-left of Zone 2: Box "AWS Certificate Manager (ACM)" with red ACM icon.
  Sub-label: "TLS Certificates (1 cert with alt names for all domains)"
Bottom-right of Zone 2: Box "Amazon Cognito" with red Cognito icon.
  Two sub-boxes: "Internal User Pool" and "Supplier User Pool".

BETWEEN ZONE 2 AND ZONE 3 (below Zone 2, outside VPC boundary):
Purple rounded box "Amazon Data Firehose (Managed service)" with purple Firehose AWS icon.
Below it: green box "S3 Events Archive (SSE-KMS)" with lock icon and label
  "(native delivery, SSE-KMS, partitioned)".

ZONE 3 — "AWS Cloud (Account) — us-east-1" (largest, ~50% width)
Light blue fill #E6F1FB, solid blue border, AWS logo top-left, bold blue label.
Inside: Large purple-bordered VPC box labeled "VPC — 3 AZs".

  VPC CONTENTS:
  
  TOP STRIP — "Public Subnets (for edge egress)"
  Light indigo fill. Contains:
  Left: Internet Gateway (navy circular icon, labeled "Internet Gateway").
  Three NAT Gateway icons evenly spaced: "NAT Gateway (AZ-a)", "NAT Gateway (AZ-b)",
  "NAT Gateway (AZ-c)". Each is a navy circular AWS icon.
  
  MIDDLE LEFT SECTION — "Private App Subnet (Application Tier)"
  Light green fill, green dashed border.
  LEFT PORTION: Orange-bordered box "ECS Cluster (Fargate)".
    Label inside top: "awsvpc mode (No public IPs)".
    Seven orange circular ECS task icons in two rows, each with white text label:
    Row 1: SIS · DFS · IMS · RE · SUP · PPS · ARS
    All seven services present — RE must be included.
  CENTER-RIGHT of Private App: Small blue box "Amazon RDS Proxy" with blue icon.
    Sub-label: "Connection pooling :5432".
    Orange dashed arrow from ECS Cluster to RDS Proxy labeled "TCP 5432".
  
  MIDDLE RIGHT SECTION — "Messaging (Managed)" (same height as Private App Subnet)
  Pink/magenta fill, pink border.
  Top box: "Amazon EventBridge Custom Event Bus" with pink EventBridge circular icon.
    Small label: "domain events (PutEvents)".
  Bottom box: "Amazon SQS (Standard + FIFO Queues)" with purple SQS circular icon.
    Two queue icons: one plain (Standard) and one FIFO icon.
  
  LOWER LEFT SECTION — "Private Data Subnet (Data Tier)"
  Light amber fill, amber border.
  LEFT: Large blue box "Amazon RDS for PostgreSQL (Multi-AZ)".
    EXACTLY TWO database cylinder icons side by side:
    Left cylinder: "Primary (AZ-a)" — darker blue.
    Right cylinder: "Standby (AZ-b)" — lighter blue.
    Double-headed orange dashed arrow between the two cylinders.
    Label below arrow: "Synchronous Replication + Auto-failover <60s".
    DO NOT show a third standby node — only two nodes total.
  RIGHT: Small green box "S3 VPC Endpoint" with green S3 icon.
  
  BOTTOM STRIP — "VPC Endpoints (Interface Endpoints)"
  Light teal fill, teal border.
  Row of nine small circular AWS service icons with labels below each:
  SQS · EventBridge · Firehose · KMS · Secrets Manager · ACM · ECR · SSM · SageMaker
  
  RIGHT COLUMN INSIDE VPC — "ML Platform (Managed)"
  Light purple fill, purple border. Three stacked boxes:
  Top: "Amazon SageMaker Training Job" with teal SageMaker icon.
  Middle: "Amazon SageMaker Batch Transform" with teal SageMaker icon.
  Bottom: "S3 ML Bucket (SSE-KMS)" with green S3 icon and lock.

  BOTTOM SECTION INSIDE AWS CLOUD (below VPC box) —
  "Monitoring & Observability (All Logs & Metrics in Amazon CloudWatch)"
  Pink fill, pink border.
  Left: Large pink "Amazon CloudWatch" box with pink CloudWatch icon.
  Right: Four sub-boxes in a row:
    "CloudWatch Logs (From ECS, API GW, CloudFront, Firehose, RDS, S3, etc.)"
    "CloudWatch Metrics"
    "CloudWatch Alarms"
    "CloudWatch Dashboards"
  Far right: Pink box "Amazon SNS" with pink SNS icon.

ZONE 4 — "External Enterprise Platforms" (rightmost, ~15% width)
Light purple fill #F4ECF7, dashed purple border.
Three boxes stacked:
  Top: "APM Platform" with graph/analytics icon.
  Middle: "Alerting Platform (PagerDuty / Slack / Email / SMS)" with bell icon, red border.
  Bottom: "Code Quality Platform" with code icon.


ARROWS — DRAW ALL OF THE FOLLOWING:

Arrow 1: Store Manager, SC Planner, Executive → CloudFront
  Three separate solid black arrows pointing right into CloudFront.
  Label: "HTTPS/443"

Arrow 2: Supplier Rep → API Gateway (bypasses CloudFront, goes directly)
  Solid black arrow pointing right to API Gateway box.
  Label: "HTTPS/443"

Arrow 3: CloudFront → MFE Static Assets S3 buckets
  Short solid arrow downward with OAC label.
  Label: "OAC (Origin Access Control)"

Arrow 4: CloudFront → API Gateway
  Solid black arrow pointing down-right from CloudFront to API Gateway.
  Label: "HTTPS/443"

Arrow 5: POS/E-Commerce → Store-Edge Aggregator
  Solid black arrow right from POS box to Aggregator.
  Label: "store LAN / batch"

Arrow 6: Store-Edge Aggregator → Amazon Data Firehose
  Solid BLUE arrow pointing right and downward from Aggregator to Firehose box.
  Label: "PutRecordBatch · IAM SigV4"

Arrow 7 [CORRECTED — Campaign Mgmt does NOT go to EventBridge directly]:
  Campaign Mgmt System → API Gateway
  Solid BLUE arrow from Campaign Mgmt box pointing right to API Gateway box.
  Label: "POST /system/v1/events · API key"
  Add small badge/annotation near the arrow: "system events stage"

Arrow 8: Firehose → S3 Events Archive
  Orange DASHED arrow pointing downward from Firehose to S3 Events Archive.
  Label: "Native delivery · SSE-KMS"

Arrow 9: Firehose → API Gateway
  Solid BLUE arrow from Firehose pointing right/upward to API Gateway.
  Label: "HTTP endpoint delivery · access key"

Arrow 10: API Gateway → ECS Cluster (via VPC Link)
  Solid BLUE arrow from API Gateway pointing right into the VPC, terminating at the
  ECS Cluster box. Passes through the VPC boundary.
  Label on arrow: "VPC Link / HTTPS"

Arrow 11 [NEW — API Gateway to EventBridge via AWS service integration]:
  API Gateway → Amazon EventBridge Custom Event Bus
  DASHED PURPLE arrow from API Gateway box, routing ABOVE the VPC boundary,
  then entering the Messaging section at the EventBridge box.
  This arrow must be visually distinct from all blue arrows.
  Label: "AWS service integration · PutEvents"
  Small annotation near the arrow midpoint: "(no VPC Link · no Lambda)"

Arrow 12: ECS Cluster → EventBridge
  Dashed BLUE arrow from ECS Cluster pointing right to EventBridge box.
  Label: "domain events (PutEvents)"

Arrow 13: EventBridge → SQS
  Short dashed BLUE arrow downward from EventBridge to SQS box.
  No label needed (implied fan-out).

Arrow 14: SQS → ECS Cluster (consumer path)
  Dashed BLUE arrow from SQS pointing left back toward ECS Cluster.
  Label: "SQS messages (Consumers)"

Arrow 15: ECS Cluster → RDS Proxy → RDS Primary
  Solid ORANGE arrow from ECS Cluster to RDS Proxy, then continuing to RDS Primary cylinder.
  Label: "TCP:5432"

Arrow 16: NAT Gateways → External Enterprise Platforms
  Dashed DARK GREY arrow from the rightmost NAT Gateway, exiting the VPC, pointing to
  the External Enterprise zone.
  Label: "Outbound Egress (HTTPS)"

Arrow 17: CloudWatch Alarms → Amazon SNS
  Solid orange arrow from CloudWatch Alarms box to SNS box.
  No label.

Arrow 18: Amazon SNS → Alerting Platform (Zone 4)
  Solid PINK arrow from SNS exiting the AWS Cloud boundary, pointing to Alerting Platform.
  Label: "L1/L2/L3 alerts"


LEGEND BOX (bottom-left of diagram, white fill, thin grey border):
  Title: "Legend"
  Six rows, each showing a line sample and a description:
  ─────────►  HTTPS (User Traffic) — solid black
  ─────────►  Direct HTTP / VPC Link (Service to Service) — solid blue
  - - - - -►  Event / Messaging Flow — dashed blue
  ─ · ─ · ─►  Data Delivery (S3 Native) / Database TCP:5432 — orange dashed
  ═ ═ ═ ═ ►  AWS Service Integration — dashed purple (THICK)
  · · · · ·►  Outbound Egress / Logs & Metrics — dark grey dashed

NOTE BOX (bottom-right of diagram, light yellow fill, thin amber border):
  Title: "Notes"
  Bullet points in small text:
  "· Store-Edge Aggregator calls Firehose directly (PutRecordBatch, IAM SigV4).
    It does NOT go via API Gateway or CloudFront."
  "· Firehose calls API Gateway via HTTP endpoint delivery (access key auth)."
  "· Campaign Mgmt System calls API Gateway (system stage, x-api-key header).
    API Gateway calls EventBridge via AWS service integration — no Lambda, no VPC Link."
  "· RDS Multi-AZ = Primary (AZ-a) + Standby (AZ-b) only — 2 nodes."
```

---

## Style Modifiers (append to primary prompt for specific models)

### For Midjourney
```
--style raw --ar 16:10 --v 6 --quality 2
professional technical architecture diagram, AWS official style,
no artistic interpretation, exact text labels, clean white background,
sharp vector-like appearance, no blur, no bokeh, no artistic effects
```

### For DALL-E / GPT-4o
```
Generate this as a precise technical diagram exactly as described.
Do not add any artistic interpretation. Render all text labels exactly
as specified. Use clean vector-illustration style. White background only.
No decorative elements. Every arrow must have its label visible.
```

### For Adobe Firefly / Stable Diffusion
```
technical diagram, architecture chart, white background, no gradients,
clean corporate illustration, AWS architecture style, precise layout,
sharp text, labeled arrows, color-coded zones, professional whitepaper quality
```

### Universal negative prompt
```
no 3D effects, no shadows, no gradients, no artistic blur, no photorealistic
elements, no people photos, no landscapes, no abstract art, no decorative
borders, no watermarks, no stock photo style, no dark background
```

---

## Known limitations of text-to-image for this diagram

Text-to-image models will render the overall composition and color zones accurately
but will struggle with:

- **Exact text labels** — service names and arrow labels may be garbled or hallucinated.
  Plan to redraw text in Figma / draw.io / Canva after generation.
- **Arrow routing** — the corrected Campaign Mgmt → API GW and API GW → EventBridge
  arrows are complex routes. Describe them to the model but verify carefully in output.
- **Icon accuracy** — AWS service icons will be approximated. Replace with official
  AWS Architecture 2024 icons in post-production.
- **Node count on RDS** — models may default to showing 3 database cylinders even when
  instructed to show 2. Regenerate if this occurs or fix in post-production.

For a production-quality diagram, use this prompt to generate the base layout and
zone structure, then overlay correct text, icons, and arrows in draw.io or Figma.
