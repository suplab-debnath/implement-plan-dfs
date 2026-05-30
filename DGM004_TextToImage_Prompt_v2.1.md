# DGM-004 — Text-to-Image Generation Prompt
## Smart Retail Platform — Data Architecture & Data Flow (v2.1 Corrected)

**Purpose:** Feed directly into a text-to-image model
**Row 1 correction:** Store-Edge Aggregator calls Firehose directly (IAM SigV4). Firehose
delivers to API Gateway via HTTP endpoint (access key). API Gateway is NOT upstream of Firehose.
**Rows 2 and 3:** Correct as-is — no changes required.

---

## Primary Prompt

```
Professional AWS data architecture and flow diagram titled
"Smart Retail Platform — Data Architecture & Flow".
White background. Clean whitepaper illustration style. No gradients. No shadows.
Arial or Helvetica font throughout. Sharp text. AWS 2024 icon set.
Color-coded horizontal swim-lane rows separated by thin divider lines.
High resolution 1500x900 landscape.

THREE HORIZONTAL SWIM-LANE ROWS stacked top to bottom, each spanning full width,
separated by bold horizontal divider lines. Each row has a large numbered label
on the far left:

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ROW 1 — INGESTION PATH (top row, light blue-grey background tint)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label on far left: circled number "1" in bold purple. Text below: "INGESTION PATH"

COMPONENTS LEFT TO RIGHT in Row 1:

Component 1 (far left):
  Dashed-border box. Icon: store/building with POS terminal.
  Label: "Store-Edge Aggregator"
  Sub-label: "per DC/Store"

Component 2 (center-left):
  Purple rounded box with AWS Firehose kinesis icon (purple circular icon).
  Label: "Amazon Data Firehose"
  Sub-label: "Managed service — outside VPC"
  IMPORTANT: This component must appear DIRECTLY to the right of the Store-Edge
  Aggregator. There is NO API Gateway or VPC Link between the Aggregator and Firehose.

From Firehose, TWO parallel output paths branch off — label them A and B:

  PATH A (upper branch from Firehose, going UP and RIGHT):
    Orange dashed arrow going diagonally up-right to an S3 bucket icon.
    S3 bucket box labeled: "S3 Bucket: smartretail-events/"
    Sub-label: "S3 Lifecycle Policy:"
    Bullet list: "Standard (30d) → IA →"
                 "Expire after 90 days"
    Arrow label: "A — Native delivery · SSE-KMS · Partition: {yyyy/MM/dd}/"

  PATH B (lower branch from Firehose, going DOWN and RIGHT):
    Blue solid arrow going diagonally down-right.
    Hits a red/navy rounded box for "Amazon API Gateway (ingest stage)".
    The API Gateway box has a small label inside:
      "HTTP endpoint · access key"
    From API Gateway, a blue arrow continues right labeled "VPC Link / HTTPS".
    This enters a blue-bordered box for "SIS ECS Service (Sales Ingestion Service)".
    Sub-label: "Fargate · Private subnet"
    Arrow label on B path: "B — Delivery by HTTP endpoint · access key"

AUTHENTICATION LABELS — CRITICAL PLACEMENT:
  The arrow FROM Store-Edge Aggregator TO Firehose must be labeled:
    "PutRecordBatch · IAM SigV4"
  This label MUST appear on the Store-Edge → Firehose arrow only.
  Do NOT place IAM SigV4 anywhere near the SIS path or Firehose output paths.

  The arrow FROM Firehose TO API Gateway (Path B) must be labeled:
    "HTTP endpoint · access key"
  The access key label belongs here, NOT near the Store-Edge Aggregator.

FROM SIS ECS SERVICE, two outbound arrows:
  Arrow 1: Solid orange arrow going RIGHT to Amazon RDS PostgreSQL box.
    Label: "INSERT sales_events + idempotency_keys"
    Small annotation below: "(same transaction)"
  Arrow 2: Dashed blue arrow going DOWN-RIGHT to Amazon EventBridge Custom Bus icon.
    Label: "SalesTransactionDomainEvent (PutEvents)"

Small dashed annotation box (light green fill) floating near the RDS connection:
  "idempotency_keys table (sales schema)"
  "event_id (PK) · received_at"
  "Scheduled cleanup: DELETE WHERE received_at > 48h"

Small dashed annotation box (orange fill) near the S3 bucket in Path A:
  "S3 Lifecycle Policy:"
  "Standard 30d → IA → Expire 90 days"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ROW 2 — OPERATIONAL HOT PATH (middle row, light orange background tint)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label on far left: circled number "2" in bold purple. Text: "OPERATIONAL HOT PATH"

COMPONENTS LEFT TO RIGHT in Row 2:

Component A (far left, large box):
  Blue bordered box with RDS PostgreSQL circular AWS icon (blue).
  Label: "Amazon RDS PostgreSQL"
  Sub-label: "Multi-AZ PostgreSQL — 6 Schemas:"
  Bullet: "sales · forecasting · inventory ·"
          "replenishment · supplier · promotions"
  Small black dashed arrow from this box labeled "CDC / Outbox (optional)"
  going right to EventBridge.

Component B (center-left):
  Pink/magenta rounded box. AWS EventBridge circular icon (pink/magenta).
  Label: "Amazon EventBridge Custom Bus"
  Sub-label: "Central event bus"
  Below the EventBridge box, a light-grey dashed rectangle showing EventBridge Rules:
    Title: "EventBridge Rules (examples)"
    Six rows with arrow symbols (→) each on its own line:
    "⊠ inventory.updated          →   IMS ECS"
    "⊠ demand.forecast.ready      →   DFS ECS"
    "⊠ replenishment.recommended  →   RE ECS"
    "⊠ supplier.score.updated     →   SUP ECS"
    "⊠ promotion.activated        →   PPS ECS"

Component C (center, vertical stack of five ECS service boxes):
  Five orange-border rounded boxes stacked vertically. Each has an orange AWS ECS
  Fargate circular icon and two text lines:
  Box 1: "IMS ECS (Inventory Management)"
  Box 2: "DFS ECS (Demand Forecasting)"
  Box 3: "RE ECS (Replenishment Engine)"
  Box 4: "SUP ECS (Supplier Management)"
  Box 5: "PPS ECS (Promotions Service)"
  Dashed blue arrows from EventBridge to each ECS box.

Component D (center-right):
  Blue-bordered rounded box. AWS ECS Fargate icon.
  Label: "ARS ECS (Analytics & Reporting Service)"
  Sub-label: "REST read queries across schemas"
  Blue arrow from ARS pointing right.

Component E (far right):
  Light green box. Mobile/tablet icon plus computer icon.
  Label: "MFE Clients"
  Sub-label: "Store Manager, SC Planner, etc."
  Arrow from ARS arriving here labeled "REST KPI responses (via API Gateway)"

Additional arrows in Row 2:
  Orange dashed arrows from each of the five ECS services pointing LEFT back to RDS.
  These show database writes from each service to its owned schema.
  Label near these arrows: "writes to owned schema"
  
  Blue dashed arrow from SIS ECS (Row 1 bottom, connecting down to EventBridge in Row 2):
    This connects the SalesTransactionDomainEvent from Row 1 into the EventBridge bus.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ROW 3 — ML TRAINING & BATCH PATH (bottom row, light green background tint)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Label on far left: circled number "3" in bold purple. Text: "ML TRAINING & BATCH PATH"

COMPONENTS LEFT TO RIGHT in Row 3 (pipeline flows left to right):

Step 1: Green S3 bucket icon.
  Label: "S3 Bucket: smartretail-events/"
  Sub-label: "Raw events (partitioned)"
  Blue arrow right labeled "Training data extraction"

Step 2: Teal box. AWS SageMaker circular icon (teal).
  Label: "Amazon SageMaker Training Job"
  Blue arrow right.

Step 3: Green S3 bucket icon.
  Label: "S3 Bucket: smartretail-ml/models/ (versioned)"
  Small annotation: "min 3 versions · manual cleanup"
  Blue arrow right.

Step 4: Teal box. AWS SageMaker circular icon.
  Label: "Amazon SageMaker Batch Transform"
  Blue arrow right.

Step 5: Green S3 bucket icon.
  Label: "S3 Bucket: smartretail-ml/batch-output/"
  Small annotation: "Expire after 30 days"
  Orange arrow right labeled "S3 Event Notification"

Step 6: Orange-bordered box. AWS Lambda icon (orange).
  Label: "Post-Processor Lambda"
  Blue arrow right labeled "ForecastWritePort"

Step 7: Orange-bordered box. AWS ECS Fargate icon.
  Label: "DFS ECS (Demand Forecasting Service)"
  Orange arrow right labeled "UPSERT demand_forecasts"

Step 8: Blue-bordered box. AWS RDS icon.
  Label: "Amazon RDS PostgreSQL (forecasting schema)"

ADDITIONAL ARROW in Row 3:
  Dashed blue curved arrow from DFS ECS going UPWARD and LEFTWARD back to EventBridge
  in Row 2, labeled: "ForecastReadyDomainEvent (PutEvents)"
  This arrow arcs back from DFS ECS to the EventBridge Custom Bus in Row 2.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEGEND AND REFERENCE BOXES (bottom of diagram, four boxes side by side)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Box 1 — "LEGEND — LINE STYLES & COLORS" (white fill, grey border):
  ────►  Data / Service Invocation (Synchronous) — solid blue
  ----►  Event / Message Flow (Asynchronous) — dashed blue
  ────►  Database Write (Transactional) — solid orange
  - - -  CDC / Replication (Optional) — dark grey dashed
  ·····  Reference / Notes — green dashed

Box 2 — "AWS ICONS — KEY" (white fill, grey border):
  Six icon+label pairs in two columns:
  Left column: Amazon Data Firehose · Amazon API Gateway · Amazon ECS (Fargate)
               Amazon RDS for PostgreSQL
  Right column: Amazon S3 · Amazon EventBridge · Amazon SageMaker · AWS Lambda

Box 3 — "S3 LIFECYCLE POLICIES" (white fill, green border):
  Three rows with green S3 bucket icons:
  "smartretail-events/:     Standard 30d → IA → Expire 90d"
  "smartretail-ml/batch-output/:  Expire after 30d"
  "smartretail-ml/models/:  Versioned (min 3 versions), manual cleanup"

Box 4 — "KEY DESIGN PRINCIPLES" (white fill, green border):
  Five checkmark bullets:
  "✓ Idempotent ingestion via idempotency_keys"
  "✓ Event-driven fan-out with EventBridge"
  "✓ Separation of hot (RDS) and cold (S3) data"
  "✓ Lifecycle policies for cost optimization"
  "✓ ML feedback loop updates forecasting schema"
```

---

## Critical Clarification for Image Generation Models

Include this as a separate instruction block when prompting:

```
CRITICAL — ROW 1 DIRECTION:
The Store-Edge Aggregator sends data DIRECTLY to Amazon Data Firehose.
There is absolutely no API Gateway between the Store-Edge Aggregator and Firehose.
The sequence in Row 1 MUST be:
  Store-Edge Aggregator → [IAM SigV4 PutRecordBatch] → Amazon Data Firehose
Then FROM Firehose there are two branches:
  Branch A → S3 (native delivery, SSE-KMS)
  Branch B → API Gateway (HTTP endpoint, access key) → VPC Link → SIS ECS
API Gateway appears as the DESTINATION of Firehose, not as a step BEFORE Firehose.
Do not reverse this order under any circumstances.
```

---

## Style Modifiers

### For Midjourney
```
--style raw --ar 5:3 --v 6 --quality 2
professional technical data architecture diagram, AWS official whitepaper style,
clean white background, color-coded swim lanes, sharp text labels, labeled arrows,
no artistic effects, no blur, no shadows, no gradients, vector illustration style
```

### For DALL-E / GPT-4o
```
Render this as a precise technical diagram. White background.
Three clearly separated horizontal swim-lane rows with divider lines.
All text labels must be sharp and legible. All arrows must have visible labels.
No artistic interpretation. The flow direction in Row 1 is left to right:
Aggregator → Firehose → two branches. Do not place API Gateway before Firehose.
```

### Universal negative prompt
```
no 3D effects, no shadows, no gradients, no photorealistic elements,
no dark background, no decorative art, no reversed flow directions,
no API Gateway between Store-Edge Aggregator and Firehose
```

---

## Row 1 Known Rendering Risk

The most likely failure mode for any image generation model on this diagram is
reversing the Store-Edge → Firehose → API Gateway sequence into
Store-Edge → API Gateway → Firehose. This is because:

1. API Gateway is visually placed closer to the left (Edge & Identity zone) in most
   AWS architecture conventions, making models associate it with "entry point".
2. The access key authentication looks like it belongs on an inbound connection
   rather than an outbound Firehose delivery.

If the generated image shows API Gateway before Firehose in Row 1, regenerate with
the Critical Clarification block included and add:
"In Row 1, Amazon Data Firehose must be the SECOND element from the left,
immediately after the Store-Edge Aggregator, with NO intermediary component."
