# LLD-DGM-006 — Text-to-Image Generation Prompt
## Messaging & Event Topology — v2.1 Corrected

**Purpose:** Feed directly into a text-to-image model
**Single correction vs current diagram:**
  Campaign Mgmt System must route through API Gateway (system stage, API key)
  THEN API Gateway calls EventBridge via AWS service integration.
  The direct Campaign Mgmt → EventBridge arrow is removed.
**Everything else is correct and preserved as-is.**

---

## Primary Prompt

```
Professional AWS event topology and messaging architecture diagram titled
"LLD-DGM-006 — Messaging & Event Topology".
White background. Clean technical whitepaper illustration style.
No gradients. No shadows. Arial or Helvetica font. Sharp legible text.
AWS 2024 service icons — colored circular icons with white symbols.
High resolution 1400x900 landscape.

THREE VERTICAL SWIM-LANE ZONES left to right, separated by thin vertical divider lines:
  Zone 1 "Sources" (~25% width, lightest purple fill #F4ECF7)
  Zone 2 "Event Backbone" (~35% width, lightest blue fill #F0F3FF)
  Zone 3 "Consumers" (~40% width, lightest yellow fill #FEFDE7)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ZONE 1 — SOURCES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Bold label "Sources" at top of zone.
Light purple fill. Four source boxes stacked top to bottom:

Box 1 (top): "Store-Edge Aggregator"
  Purple-border rounded box. Store/building icon.
  Small "NEW" badge top-left corner (green pill).
  Black solid arrow pointing RIGHT to Amazon Data Firehose in Zone 2.
  Arrow label: "PutRecordBatch · SigV4"

Box 2: "POS / eCommerce Platform"
  Purple-border rounded box. Shopping cart + POS terminal icon.
  Small black arrow pointing UP to Store-Edge Aggregator (feeds aggregator).
  No direct arrow to Zone 2.

Box 3: "Campaign Mgmt System"
  Purple-border rounded box. Megaphone/broadcast icon.
  [CORRECTED] Blue arrow pointing RIGHT to Amazon API Gateway in Zone 2.
  Arrow label: "PromotionActivated (HTTP/HTTPS · API key)"
  Small annotation near arrow: "system events stage"
  DO NOT draw an arrow directly from Campaign Mgmt to Amazon EventBridge.
  The arrow MUST go to API Gateway first.

Box 4 (bottom): "ECS Services (domain event publishers)"
  Purple-border rounded box. AWS ECS Fargate icon (orange hexagon cluster).
  Blue dashed arrow pointing RIGHT to Amazon EventBridge in Zone 2.
  Arrow label: "domain events"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ZONE 2 — EVENT BACKBONE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Bold label "Event Backbone" at top of zone.
Light blue fill. Three components stacked:

Component A (top): "Amazon Data Firehose"
  Large purple-bordered rounded box with purple AWS Firehose icon.
  Small "NEW" badge top-left corner (green pill).
  Inside box, two bullet points in small text:
    "· Dual delivery: S3 (archive) + API GW (SIS processing)"
    "· Edge Aggregator → Firehose · No Kinesis Consumer Lambda"
  TWO arrows leaving Firehose:
  Arrow A1: Orange dashed arrow going UP-RIGHT to Amazon S3 box (top-right corner of Zone 2).
    Arrow label: "native delivery"
    Amazon S3 box: small green S3 icon, label "Amazon S3 (archive)"
  Arrow A2: Black solid arrow going DOWN to Amazon API Gateway below.
    Arrow label: "HTTP endpoint · VPC Link"

Component B (center): "Amazon API Gateway"
  Dark navy rounded box with AWS API Gateway icon.
  [CORRECTED] This box receives from TWO sources:
    - From Firehose above (Arrow A2) — HTTP endpoint delivery
    - From Campaign Mgmt System in Zone 1 (blue arrow) — system events stage
  TWO arrows leaving API Gateway:
  Arrow B1: Black solid arrow going RIGHT into Zone 3, terminating at SIS ECS.
    Arrow label: "HTTP endpoint · VPC Link"
  Arrow B2: [CORRECTED NEW] Dashed purple arrow going DOWN to Amazon EventBridge.
    Arrow label: "AWS service integration → PutEvents"
    Small annotation: "(no VPC Link · no Lambda)"
    This arrow must be visually distinct from the blue EventBridge arrows.

Component C (bottom): "EventBridge Custom Bus"
  Large pink/magenta-bordered rounded box with pink AWS EventBridge icon.
  Label: "Amazon EventBridge"
  Sub-label: "EventBridge Custom Bus"
  Inside box: list of 8 routing rules with numbered circles:
    "8 routing rules:"
    "① Sales transaction event → IMS queue"
    "② Sales transaction event → DFS queue"
    "③ Forecast event → IMS forecast queue"
    "④ Forecast event → ARS updates queue"
    "⑤ Inventory alert event → RE alert queue (FIFO)"
    "⑥ Purchase order event → SUP queue"
    "⑦ Promotion adjustment event → DFS adjustment queue"
    "⑧ Promotion activation event → PPS queue"
  Multiple blue dashed arrows fanning out from EventBridge going RIGHT into Zone 3,
  one arrow per SQS queue (8 arrows total).
  Arrow label group: "fan-out (EventBridge rules → SQS)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ZONE 3 — CONSUMERS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Bold label "Consumers" at top of zone.
Light yellow fill. Two sub-sections:

SUB-SECTION A — "SQS Queues (each with DLQ)" (upper portion, blue-dashed border)
  Eight SQS queue rows stacked vertically. Each row has three elements:
    SQS Queue icon → ECS Service icon → DLQ icon
  Arrows:
    Blue solid arrow from SQS to ECS: "poll"
    Red dashed arrow from SQS to DLQ: "on failure"
  The eight rows (top to bottom):
    Row 1: IMS sales queue → IMS ECS → IMS sales DLQ
    Row 2: DFS sales queue → DFS ECS → DFS sales DLQ
    Row 3: IMS forecast queue → IMS ECS → IMS forecast DLQ
    Row 4: ARS updates queue → ARS ECS → ARS updates DLQ
    Row 5: RE alert queue FIFO → RE ECS → RE alert DLQ (FIFO)
    Row 6: SUP queue → SUP ECS → SUP DLQ
    Row 7: DFS adjustment queue → DFS ECS → DFS adjustment DLQ
    Row 8: PPS inbound queue → PPS ECS → PPS inbound DLQ

  Each SQS icon is a purple circular AWS icon with queue symbol.
  Each ECS icon is an orange circular AWS Fargate icon.
  Each DLQ icon is a red circular queue icon with "DLQ" label.
  Queue labels are bold. DLQ labels use red text.

SUB-SECTION B — "Direct Firehose Delivery (via API GW)" (lower portion, blue-dashed border)
  Single ECS row, visually separated from the EventBridge fan-out section above.
  Large blue ECS icon. Label: "SIS ECS"
  Sub-label: "(Receives from Firehose via API GW,"
  Sub-label 2: "not from EventBridge fan-out)"
  Blue solid arrow arriving from Zone 2 (from API Gateway) pointing to SIS ECS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEGEND (bottom strip, spanning full width)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
White fill, grey border. Seven legend entries in a horizontal row:
  ─────►  PutRecordBatch · SigV4 — solid black
  · · · ►  native delivery — orange dashed
  ─────►  HTTP endpoint · VPC Link — solid black (thick)
  - - - ►  PromotionActivated / domain events — blue dashed
  - = - ►  AWS service integration → EventBridge — purple dashed (NEW)
  - - - ►  fan-out (EventBridge rules → SQS) — blue dashed
  ─────►  poll (SQS → ECS) — solid black (thin)
  - - - ►  on failure (SQS → DLQ) — red dashed
```

---

## Critical Clarification for Image Generation Models

```
CRITICAL — CAMPAIGN MGMT ARROW:
Campaign Mgmt System in Zone 1 must NOT have an arrow going directly
to Amazon EventBridge. The arrow from Campaign Mgmt must go to
Amazon API Gateway in Zone 2. Then a SEPARATE arrow from API Gateway
goes to Amazon EventBridge. These are two distinct arrows with different
labels and different visual styles:
  Arrow 1: Campaign Mgmt → API Gateway  (solid blue, "PromotionActivated · API key")
  Arrow 2: API Gateway → EventBridge    (dashed purple, "AWS service integration → PutEvents")
This is the only correction from the v1.0 diagram. Everything else is unchanged.
```

---

## Style Modifiers

### For Midjourney
```
--style raw --ar 3:2 --v 6 --quality 2
professional AWS event-driven architecture topology diagram, messaging diagram,
whitepaper illustration style, white background, three vertical swim lanes,
color-coded zones, sharp text labels, numbered routing rules, labeled arrows,
no artistic effects, no shadows, no gradients
```

### For DALL-E / GPT-4o
```
Render this as a precise technical messaging topology diagram. Three vertical
swim lanes: Sources (light purple), Event Backbone (light blue), Consumers
(light yellow). Eight SQS queue rows each with a queue icon, ECS icon, and DLQ icon.
Campaign Mgmt System arrow goes to API Gateway, not directly to EventBridge.
API Gateway then has a separate dashed purple arrow to EventBridge labeled
"AWS service integration". All text sharp and legible. White background.
```

### Universal negative prompt
```
no Campaign Management arrow going directly to EventBridge,
no direct CMS to EventBridge connection, no Kinesis Data Streams,
no Lambda consumer between Firehose and ECS, no DynamoDB,
no 3D effects, no shadows, no gradients, no dark background
```

---

## Corrections vs Current Diagram

| # | Element | Current (wrong) | Corrected |
|---|---|---|---|
| 1 | Campaign Mgmt arrow | Direct dashed arrow to Amazon EventBridge labeled "PromotionActivated" | Blue solid arrow to API Gateway labeled "PromotionActivated (HTTP · API key)" + NEW dashed purple arrow from API Gateway to EventBridge labeled "AWS service integration → PutEvents" |
| 2 | API Gateway connections | Receives only from Firehose | Receives from Firehose (HTTP endpoint) AND from Campaign Mgmt (system events stage) |
| 3 | draw.io prompt line | `Campaign Mgmt → EventBridge: dashed blue "PromotionActivated"` | Replace with two lines: `Campaign Mgmt → API Gateway: solid blue "system events · API key"` and `API Gateway → EventBridge: dashed purple "AWS service integration → PutEvents"` |

All other elements are correct and preserved unchanged.
