# LLD-DGM-018 — Text-to-Image Generation Prompt
## SmartRetail — Disaster Recovery Architecture (Pilot Light — us-west-2)

**LLD Section:** §5.2 Disaster Recovery Architecture
**Strategy:** Pilot Light — minimal resources always running in DR region
**RTO Target:** < 60 minutes | **RPO Target:** < 30 minutes
**Primary:** us-east-1 (Active) | **DR:** us-west-2 (Pilot Light — idle)
**Status:** New diagram — no prior version

---

## Primary Prompt

```
Professional AWS disaster recovery architecture diagram titled
"LLD-DGM-018 — Disaster Recovery Architecture (Pilot Light)".
Sub-title: "Primary: us-east-1 (Active) · DR: us-west-2 (Pilot Light · Idle)"
White background. Clean technical whitepaper illustration style.
AWS 2024 service icons. Sharp legible text. No gradients. No shadows.
High resolution 1500x900 landscape.

OVERALL LAYOUT: Four horizontal bands top to bottom:
  Band 1 (top): Route 53 + health check — spans full width
  Band 2 (middle, largest): Two region boxes side by side
    LEFT region box (~45%): PRIMARY — us-east-1 (Active)
    CENTER gap (~10%): Cross-region replication arrows
    RIGHT region box (~45%): DR — us-west-2 (Pilot Light)
  Band 3 (below regions): DR Activation Steps — numbered flow, full width
  Band 4 (bottom): Legend + Recovery Targets panel

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BAND 1 — ROUTE 53 (top strip, full width)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Light blue fill strip. Label: "Amazon Route 53 — Failover Routing"

Three elements in a row:
Left: Route 53 circular AWS icon (blue).
  Label: "Amazon Route 53"
  Sub-label: "Failover routing policy"
Center: Health check icon (green tick / red X toggle visual).
  Label: "Health Check"
  Sub-label: "api.smartretail.com"
  Two arrows from Route 53 health check pointing DOWN:
    LEFT arrow → Primary region box below (solid green arrow).
      Arrow label: "PRIMARY (active)"
    RIGHT arrow → DR region box below (orange dashed arrow).
      Arrow label: "FAILOVER (activates on primary failure)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BAND 2 LEFT — PRIMARY REGION: us-east-1 (ACTIVE)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Large rounded box. Light green fill. Solid green border 2px.
Label top-left: AWS icon + "PRIMARY REGION — us-east-1"
Green "ACTIVE" badge pill top-right corner of the box.

Inside, four stacked rows of components:

ROW 1 — Edge layer:
  "Amazon API Gateway" box — dark navy icon. Label: "Fully active · all traffic"
  "Amazon Data Firehose" box — purple icon. Label: "Dual delivery active"

ROW 2 — Compute layer:
  "Amazon ECS Cluster (Fargate)" — large orange-bordered box.
  Inside: 7 orange ECS task icons labeled: IMS · DFS · RE · SUP · PPS · ARS · SIS
  Sub-label: "All 7 services running · min 2 tasks each · 3 AZs"
  "Amazon RDS Proxy" box — blue icon.

ROW 3 — Data layer:
  "Amazon RDS Primary" — blue database icon.
  Label: "PostgreSQL Multi-AZ"
  Sub-label: "AZ-a (Primary) + AZ-b (Standby)"
  Sub-label 2: "Continuous async replication → us-west-2 read replica"
  "Amazon S3" — two green bucket icons.
  Labels: "smartretail-events/" and "smartretail-ml/"
  Sub-label: "S3 CRR enabled → us-west-2"

ROW 4 — Messaging + Observability:
  "EventBridge + SQS" — pink and purple icons.
  Label: "Domain events · fan-out"
  "Amazon CloudWatch" — orange icon.
  Label: "Health alarms · triggers DR on failure"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BAND 2 CENTER — CROSS-REGION REPLICATION ARROWS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Vertical strip between the two region boxes.
Four horizontal DASHED arrows pointing LEFT to RIGHT (Primary → DR):

Arrow 1 (RDS):
  Blue dashed arrow at RDS level.
  Label: "RDS cross-region read replica"
  Sub-label: "async · lag typically < 5 min"
  Small clock icon: "RPO contribution: ~5 min"

Arrow 2 (S3):
  Green dashed arrow at S3 level.
  Label: "S3 Cross-Region Replication (CRR)"
  Sub-label: "events/ and ml/ buckets"
  Small clock icon: "RPO contribution: < 15 min"

Arrow 3 (ECR):
  Orange dashed arrow at compute level.
  Label: "ECR image push (at deploy time)"
  Sub-label: "Both regions get images on every release"

Arrow 4 (Parameter Store / Config):
  Grey dashed arrow.
  Label: "CDK context · SSM parameters"
  Sub-label: "Pre-deployed in us-west-2"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BAND 2 RIGHT — DR REGION: us-west-2 (PILOT LIGHT)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Large rounded box. Light amber/orange fill. Amber dashed border 2px.
Label top-left: AWS icon + "DR REGION — us-west-2"
Amber "PILOT LIGHT — IDLE" badge pill top-right corner.
Small sub-label: "Pre-provisioned · zero ECS tasks running"

Inside, same four rows mirroring the primary — but with IDLE indicators:

ROW 1 — Edge layer (pre-configured, not receiving traffic):
  "Amazon API Gateway" box — dark navy icon, semi-transparent/greyed.
  Label: "Pre-configured"
  Sub-label: "Not receiving traffic · activates on failover"
  "Amazon Data Firehose" box — purple icon, semi-transparent.
  Label: "Pre-configured · idle"

ROW 2 — Compute layer (0 tasks running):
  "Amazon ECS Cluster (Fargate)" — amber-bordered box.
  Inside: Dashed empty boxes where task icons would be, labeled "0 tasks".
  Sub-label: "Task definitions registered · NO running tasks"
  Sub-label 2: "Tasks start on DR activation (~10 min cold start)"
  "Amazon RDS Proxy" box — blue icon, semi-transparent.
  Label: "Pre-configured · idle"
  "Amazon ECR" — green box. Label: "Images mirrored from us-east-1"

ROW 3 — Data layer (read replica — always running):
  "Amazon RDS Read Replica" — blue database icon, SOLID (always running).
  Label: "PostgreSQL Read Replica"
  Sub-label: "Always running · receiving replication from us-east-1"
  Sub-label 2: "Promoted to standalone on DR activation (~2-5 min)"
  Green solid border on this box to indicate it IS always running.
  "Amazon S3" — two green bucket icons, SOLID.
  Labels: "smartretail-events/" and "smartretail-ml/"
  Sub-label: "CRR destination · always in sync"

ROW 4 — Messaging + Config (pre-provisioned, not active):
  "EventBridge + SQS" — pink and purple icons, semi-transparent.
  Label: "Pre-provisioned · not routing events"
  "VPC + Subnets + SGs" — network icon.
  Label: "Pre-provisioned via CDK"
  Sub-label: "10.1.0.0/16 · 3 AZs"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BAND 3 — DR ACTIVATION STEPS (numbered flow, full width)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Light orange fill strip. Label: "DR ACTIVATION SEQUENCE (estimated total: 45-60 minutes)"

Eight numbered steps in a horizontal pipeline with right-pointing arrows between each:

Step ① (0 min): Red hexagon icon.
  Label: "Primary Failure"
  Sub-label: "CloudWatch alarm fires"

Step ② (0-5 min): Route 53 icon.
  Label: "DNS Failover"
  Sub-label: "Route 53 health check fails · failover record activates"

Step ③ (5-10 min): CDK/CodePipeline icon.
  Label: "CDK Deploy"
  Sub-label: "ECS services started in us-west-2"

Step ④ (5-8 min): Database icon.
  Label: "RDS Promotion"
  Sub-label: "Read replica promoted to standalone primary"

Step ⑤ (10-20 min): ECS icon.
  Label: "ECS Warm-up"
  Sub-label: "Tasks start · health checks pass"

Step ⑥ (20-30 min): Connectivity icon.
  Label: "Service Connect"
  Sub-label: "ECS connects to promoted RDS via RDS Proxy"

Step ⑦ (30-45 min): API Gateway icon.
  Label: "Traffic Live"
  Sub-label: "API Gateway us-west-2 accepting requests"

Step ⑧ (45-60 min): Green tick icon.
  Label: "DR Complete"
  Sub-label: "Full stack operational in us-west-2"

Timeline bar below the steps:
  A horizontal bar with time markers: 0 min · 10 min · 20 min · 30 min · 45 min · 60 min
  Green zone from 0-60 labeled "Within RTO target (< 60 min)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BAND 4 — RECOVERY TARGETS AND LEGEND
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Three boxes side by side spanning full width:

Box 1 — RECOVERY TARGETS (green border, white fill):
  Large clock icon.
  "RTO (Recovery Time Objective)"
  Bold large text: "< 60 minutes"
  Sub: "(AZ-to-region failover: ECS cold start + RDS promotion + DNS TTL)"
  
  Database icon below.
  "RPO (Recovery Point Objective)"
  Bold large text: "< 30 minutes"
  Sub: "(RDS read replica lag ~5 min · S3 CRR < 15 min · in-flight events ~5 min)"

Box 2 — ALWAYS-RUNNING IN DR (amber border):
  Title: "Pilot Light — Always Running (us-west-2)"
  Checkmark list:
  "✓ VPC + subnets + security groups (CDK pre-deployed)"
  "✓ RDS Read Replica (receiving continuous replication)"
  "✓ S3 buckets (CRR destination, always synced)"
  "✓ ECR repositories (images mirrored at deploy time)"
  "✓ API Gateway + Firehose (pre-configured, not active)"
  "✗ ECS tasks (0 running — started on DR activation)"

Box 3 — LEGEND (grey border):
  Six arrow samples with labels:
  Solid green → Active traffic flow (PRIMARY)
  Amber dashed → Failover / DR activation trigger
  Blue dashed → RDS cross-region replication (async)
  Green dashed → S3 CRR replication
  Orange dashed → CDK / config replication
  Grey component → Idle / pre-configured (not receiving traffic)
  Green-bordered component → Always running (even in idle state)
```

---

## Critical Clarifications for Image Generation Models

```
CRITICAL 1 — PILOT LIGHT STATE MUST BE VISUALLY DISTINCT:
Components in the DR region (us-west-2) that are IDLE must appear greyed-out
or semi-transparent: ECS tasks (show 0 tasks), API Gateway, Firehose, EventBridge,
SQS, RDS Proxy. These are pre-configured but NOT running.
Components that ARE always running in the DR region must appear SOLID and
green-bordered: RDS Read Replica and S3 buckets (they receive continuous replication).

CRITICAL 2 — RDS READ REPLICA VS STANDBY:
In the DR region (us-west-2), the RDS component is a READ REPLICA
(cross-region, async replication), NOT a Multi-AZ standby.
The Multi-AZ standby is within the PRIMARY region (us-east-1 AZ-b).
The read replica is in us-west-2 and is promoted to standalone on DR activation.

CRITICAL 3 — ACTIVATION SEQUENCE IS LEFT TO RIGHT:
The 8 numbered activation steps (Band 3) must flow left to right with
connecting arrows between each step. This is a timeline, not a loop.
The timeline bar at the bottom shows 0-60 minutes total duration.
```

---

## Pilot Light vs Other DR Strategies — Context Note

Include this as an annotation box in the diagram if space allows:

```
Pilot Light vs alternatives:
  Backup & Restore:  RTO ~4h  · RPO ~1h  · lowest cost
  Pilot Light:       RTO ~1h  · RPO 30min · this design
  Warm Standby:      RTO ~15m · RPO ~5min · ~2x cost
  Multi-Site Active: RTO ~0   · RPO ~0   · ~4x cost
```

---

## Style Modifiers

### For Midjourney
```
--style raw --ar 5:3 --v 6 --quality 2
professional AWS disaster recovery architecture diagram, pilot light DR pattern,
two region boxes side by side with replication arrows, numbered activation
sequence pipeline at bottom, whitepaper technical illustration style,
white background, no artistic effects, no shadows, no gradients,
sharp text labels, color-coded active vs idle components
```

### For DALL-E / GPT-4o
```
Render this as a precise technical disaster recovery diagram. White background.
Two large region boxes side by side: Primary us-east-1 (green, ACTIVE) on the
left and DR us-west-2 (amber, PILOT LIGHT IDLE) on the right. Four cross-region
replication arrows in the center gap. Numbered 8-step activation sequence as a
horizontal pipeline at the bottom with a timeline bar. Recovery Targets panel
showing RTO < 60 min and RPO < 30 min. Idle DR components must appear
semi-transparent or greyed. RDS Read Replica and S3 buckets in DR region
must appear solid/active since they are always running.
```

### Universal negative prompt
```
no equal-sized active components in both regions, no multi-site active-active design,
no RDS Multi-AZ standby in the DR region (it is a read replica, not a standby),
no running ECS tasks in the DR region, no Campaign Mgmt to EventBridge direct,
no Kinesis Data Streams, no DynamoDB, no 3D effects, no shadows, no gradients
```
