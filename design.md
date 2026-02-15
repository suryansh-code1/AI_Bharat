# Design Document

## AI-MartIntel – AI-Driven Retail Intelligence for Bharat

---

### Design Overview

AI-MartIntel is a cloud-native, hybrid-edge AI platform built on AWS that delivers retail intelligence to Indian retailers through mobile-first interfaces with vernacular language support. The system operates on a fundamental architectural principle: **two automatic data streams — digital (billing/POS) and physical (CCTV vision) — are captured without retailer effort, fused by AI, and delivered as plain-language answers on the retailer's phone.**

The platform combines edge computing (for privacy-preserving in-store vision processing) with cloud-native serverless architecture (for scalable AI/ML, data storage, and insight delivery). It leverages Amazon Bedrock for natural language generation, SageMaker for predictive models, and custom computer vision models running on low-cost edge devices for in-store behavior analytics.

**Core Design Principles:**

1. **Automatic-First**: Data flows into the system from billing systems and cameras without any manual action from the retailer
2. **Edge + Cloud Hybrid**: Vision AI runs on-premise (privacy by architecture); everything else runs serverless on AWS
3. **Fusion Intelligence**: The system's core differentiator is correlating digital sales data with physical movement data to produce insights neither source can generate alone
4. **Answer-First UI**: Every output is a specific, actionable recommendation in the retailer's language — not a chart, not a table, not raw data
5. **Privacy by Architecture**: No facial recognition, no identity tracking, no raw video leaves the store — this is structural, not policy-based
6. **Mobile-First, Low-Bandwidth Ready**: Optimized for Indian smartphones on 3G networks with offline capability
7. **Tiered Capability**: Same platform serves kirana stores (Lite), supermarkets (Standard), and brand chains (Enterprise) with appropriate depth

---

### High-Level Architecture

The system follows a four-layer hybrid architecture:

**Layer 1 — Edge Processing Layer (In-Store):**

- Edge devices (Raspberry Pi 4 / Jetson Nano / local PC) connected to existing CCTV cameras
- Runs lightweight computer vision models (YOLOv8-nano) for person detection and zone tracking
- Computes footfall, dwell time, crowd density, and queue metrics locally
- Transmits ONLY anonymized numerical metadata to cloud — no video, no images, no frames leave the store

**Layer 2 — Data Ingestion Layer (Automatic):**

- Direct API integrations with Indian POS/billing platforms (PetPooja, Posist, Mswipe, BharatPe, Vyapar)
- Webhook listeners and lightweight desktop sync agents for billing software
- AWS IoT Core for edge device metadata streaming
- Schema normalization pipeline that converts any POS format to a canonical transaction schema
- CSV/Excel upload as clearly labeled secondary fallback only

**Layer 3 — Intelligence Layer (Cloud AI/ML):**

- Amazon SageMaker for demand forecasting, pricing optimization, and customer segmentation models
- Custom Fusion Engine that correlates sales data with zone traffic data
- Amazon Bedrock (Claude/Titan) for generating natural-language insights in vernacular languages
- Context enrichment pipeline (festivals, weather, day-of-week, seasonal signals)
- Automated model retraining and A/B testing via Step Functions

**Layer 4 — Presentation Layer (Mobile-First):**

- Progressive Web App (PWA) with offline capability
- Answer-first interface: actionable recommendations, not charts
- Real-time alerts via push notifications, SMS, and WhatsApp (Amazon SNS)
- Vernacular language interface (6+ Indian languages)
- Enterprise desktop dashboard for multi-store management

---

### System Architecture Diagram

```text
IN-STORE (EDGE LAYER)
======================

+-------------------+         +-------------------------------------+
|  Existing CCTV    | RTSP    |  Edge Device                        |
|  Camera(s)        |-------->|  (Raspberry Pi 4 / Jetson Nano)     |
|  (>=720p, any     |         |                                     |
|   brand)          |         |  Vision Processing Pipeline:        |
+-------------------+         |  - Frame extraction (1-2 FPS)       |
                              |  - YOLOv8-nano person detection     |
                              |  - Centroid tracking                |
+-------------------+         |  - Zone assignment                  |
|  POS / Billing    |         |  - Dwell time calculation           |
|  System           |         |  - Queue length estimation          |
|  (PetPooja,       |         +------------------+------------------+
|   Vyapar, etc.)   |                            |
+--------+----------+              Anonymized metadata ONLY:
         |                         {zone_id, person_count,
         |                          avg_dwell, max_occupancy,
         |                          queue_length, timestamp}
         |                                       |
         |        NO RAW VIDEO LEAVES THE STORE  |
=========|=======================================|====================
         |            AWS CLOUD                  |
         |                                       |
         v                                       v
+--------------------+              +------------------------+
|  API Gateway /     |              |  AWS IoT Core          |
|  Webhook Listener  |              |  (Edge Metadata        |
|  (POS Transaction  |              |   Ingestion)           |
|   Streaming)       |              +----------+-------------+
+--------+-----------+                         |
         |                                     |
         v                                     v
+----------------------------------------------------------+
|  Lambda: Data Normalization Pipeline                      |
|  - Schema validation                                      |
|  - POS adapter layer (multi-format support)               |
|  - Duplicate detection (composite key)                    |
|  - Canonical format conversion                            |
+----------------------------+-----------------------------+
                             |
              +--------------+--------------+
              |                             |
              v                             v
+---------------------+      +------------------------+
|  Amazon S3          |      |  Amazon DynamoDB        |
|  (Data Lake)        |      |  Tables:                |
|                     |      |  - Stores               |
|  /raw/              |      |  - Transactions         |
|    transactions/    |      |  - SKUs                 |
|    vision-meta/     |      |  - ZoneMetrics          |
|  /processed/        |      |  - Forecasts            |
|    training-data/   |      |  - FusionScores         |
|    feature-store/   |      |  - Alerts               |
|  /models/           |      |  - Users                |
|    artifacts/       |      +------------------------+
+---------------------+               |
              |                        |
              v                        v
+----------------------------------------------------------+
|                 INTELLIGENCE LAYER                        |
|                                                           |
|  +----------------+  +---------------+  +--------------+ |
|  | SageMaker:     |  | SageMaker:    |  | SageMaker:   | |
|  | Demand         |  | Pricing       |  | Customer     | |
|  | Forecasting    |  | Optimization  |  | Segmentation | |
|  | (DeepAR /      |  | (Elasticity   |  | (K-means,    | |
|  |  LightGBM)     |  |  + RL)        |  |  FP-Growth)  | |
|  +-------+--------+  +-------+-------+  +------+-------+ |
|          |                    |                  |         |
|          +--------------------+------------------+         |
|                               |                            |
|                    +----------v-----------+                |
|                    |  FUSION ENGINE        |                |
|                    |                       |                |
|                    |  Correlates:          |                |
|                    |  - Sales per zone     |                |
|                    |  - Traffic per zone   |                |
|                    |  - Dwell per zone     |                |
|                    |                       |                |
|                    |  Outputs:             |                |
|                    |  - Zone conversion    |                |
|                    |  - Attention-to-      |                |
|                    |    purchase ratio     |                |
|                    |  - Placement recs     |                |
|                    +----------+-----------+                |
|                               |                            |
|                    +----------v-----------+                |
|                    |  Amazon Bedrock       |                |
|                    |  (Claude / Titan)     |                |
|                    |                       |                |
|                    |  Model outputs -->    |                |
|                    |  plain language       |                |
|                    |  insights in Hindi,   |                |
|                    |  Kannada, Tamil,      |                |
|                    |  Telugu, Bengali,     |                |
|                    |  Marathi              |                |
|                    +----------+-----------+                |
|                               |                            |
|  +----------------------------------------------------+   |
|  |  Context Enrichment (automatic, no user input)     |   |
|  |  - Indian festival calendar (all religions)        |   |
|  |  - Weather API (OpenWeatherMap)                    |   |
|  |  - Day/week/month/season signals                   |   |
|  |  - School/college schedules                        |   |
|  +----------------------------------------------------+   |
+----------------------------------------------------------+
              |
              v
+----------------------------------------------------------+
|                 PRESENTATION LAYER                        |
|                                                           |
|  +----------------+  +---------------+  +--------------+ |
|  | API Gateway    |  | Amazon SNS    |  | CloudFront   | |
|  | (REST APIs)    |  | (Push / SMS / |  | (CDN +       | |
|  |                |  |  WhatsApp)    |  |  Static      | |
|  +-------+--------+  +-------+-------+  |  Assets)     | |
|          |                    |           +------+-------+ |
|          +--------------------+------------------+         |
|                               |                            |
|                    +----------v-----------+                |
|                    |  PWA Frontend         |                |
|                    |  React / Next.js      |                |
|                    |  Mobile-first         |                |
|                    |  Answer-first UI      |                |
|                    |  Offline capable      |                |
|                    |  6+ languages         |                |
|                    +----------------------+                |
+----------------------------------------------------------+


---
```
##Component Breakdown — Edge Layer, Data Ingestion, Storage

## Component Breakdown

### 1. Edge Processing Layer (In-Store Vision)

**Purpose:** Process CCTV camera feeds on-premise to extract anonymized movement intelligence without any visual data leaving the store.

#### 1.1 Edge Device Hardware

| Tier | Recommended Device | Cost | Capability |
|---|---|---|---|
| Lite | Raspberry Pi 4 (4GB RAM) | ~₹5,000 | Single camera, basic footfall + peak hour |
| Standard | Raspberry Pi 4 (8GB) or Jetson Nano | ~₹8,000–15,000 | 2–4 cameras, full zone tracking + dwell |
| Enterprise | Jetson Nano / Orin Nano or dedicated mini-PC | ~₹15,000–30,000 | 6+ cameras, real-time multi-zone + queue + staff |

All devices are pre-flashed with AI-MartIntel edge software before deployment. Plug-and-play setup.

#### 1.2 Vision Processing Pipeline (Runs on Edge Device)

**Step-by-step processing flow:**

1. **Frame Extraction**: Camera RTSP feed sampled at 1–2 FPS (not full video)
2. **Person Detection**: YOLOv8-nano (quantized for ARM/edge) outputs bounding boxes with confidence scores
3. **Centroid Tracking**: SORT or ByteTrack lightweight tracker assigns per-person centroid position per frame
4. **Zone Assignment**: Each centroid mapped to pre-defined zone grid (configured during onboarding via overlay tool)
5. **Metric Computation**: Per zone, per 5-minute window:
   - `person_count` — unique persons who entered the zone
   - `avg_dwell_seconds` — mean time spent in zone
   - `max_occupancy` — peak simultaneous persons
   - `queue_length` — for designated checkout zones
6. **Metadata Transmission**: Anonymized metadata packet sent via HTTPS POST to AWS IoT Core or API Gateway

**What is transmitted (numbers only):**

```text
{
  "store_id": "store_042",
  "zone_id": "aisle_1",
  "window_start": "2025-01-15T18:00:00+05:30",
  "window_end": "2025-01-15T18:05:00+05:30",
  "person_count": 14,
  "avg_dwell_seconds": 38.5,
  "max_occupancy": 6,
  "queue_length": null
}
```
**What is NOT transmitted:** No frames. No images. No video. No bounding boxes. No appearance features.

---

#### 1.3 Edge Software Architecture

- **Runtime:** Python 3.10+ with OpenCV, ONNX Runtime (for model inference)
- **Model:** YOLOv8-nano exported to ONNX, INT8 quantized for edge performance
- **Memory footprint:** Less than 500MB RAM sustained
- **CPU usage:** Less than 70% on Raspberry Pi 4 at 1 FPS
- **Local buffer:** 1 hour of metadata cached locally if cloud connectivity is lost; auto-syncs on reconnection
- **Remote management:** Heartbeat signal every 60 seconds to cloud; remote software update capability via secure OTA channel
- **Auto-recovery:** Watchdog process restarts pipeline on crash; no data loss due to local buffer

---

#### 1.4 Zone Definition Tool

During store onboarding:

1. Edge device captures a single still frame from each camera
2. Still frame is uploaded to a web-based configuration tool
3. Onboarding operator overlays a grid on the image and labels each cell (e.g., "Entrance," "Aisle 1," "Billing Counter," "Back Shelf")
4. Zone map is saved and pushed to edge device
5. Total setup time: 15 minutes or less per camera

The tool requires no technical knowledge. Drag-and-drop interface with visual confirmation.

---

#### 1.5 Privacy Data Flow Summary

| Data Category | Stays In-Store | Sent to Cloud |
| --- | --- | --- |
| Raw video feed | Yes | No |
| Individual frames or images | Yes | No |
| Bounding box coordinates | Yes (processed, not stored) | No |
| Person appearance or features | Never captured | No |
| Facial data | Never captured at all | No |
| Zone-level aggregate counts | No | Yes (anonymized) |
| Dwell time averages | No | Yes (anonymized) |
| Queue length estimates | No | Yes (anonymized) |

---

### 2. Data Ingestion Layer (Automatic-First)

**Purpose:** Capture all sales and transaction data automatically from billing systems without retailer effort.

---

#### 2.1 Primary Ingestion: POS and Billing Integration (Default)

**Integration methods supported per platform:**

| Method | Description | Use Case |
| --- | --- | --- |
| REST API polling | Scheduled pull every 15 minutes | Cloud-based POS platforms |
| Webhook listener | Real-time push from POS | POS platforms with webhook support |
| Desktop sync agent | Lightweight agent monitors local billing software exports | Offline or desktop billing software |

**Processing pipeline:**

1. Transaction data arrives at API Gateway or IoT Core
2. Lambda POS Adapter Layer detects source platform and applies platform-specific adapter
3. Data normalized to canonical schema: store_id, timestamp, sku_id, sku_name, category, quantity, unit_price, total
4. Validation checks for completeness and data quality
5. Duplicate detection using composite key: store_id + timestamp + sku_id + total
6. Raw data archived to S3; normalized records written to DynamoDB Transactions table

**POS Adapter Design:**

Each POS platform has a dedicated adapter module implementing a common interface. The adapter directory structure is as follows:

- adapters/petpooja_adapter.py
- adapters/posist_adapter.py
- adapters/mswipe_adapter.py
- adapters/bharatpe_adapter.py
- adapters/vyapar_adapter.py
- adapters/generic_csv_adapter.py (fallback)
- adapters/adapter_interface.py (common interface)

**Adapter Interface (conceptual):**

- authenticate(credentials) returns session
- fetch_transactions(session, since_timestamp) returns raw data
- normalize(raw_data) returns list of canonical transactions
- validate(transactions) returns validation result

New POS platforms are added by writing a single adapter. No core pipeline changes required.

**Latency target:** Transaction available for AI processing within 5 minutes of occurrence at POS.

---

#### 2.2 Secondary Ingestion: Vision Metadata from Edge Devices

1. Edge device sends HTTPS POST every 5 minutes (or MQTT via IoT Core for persistent connection)
2. IoT Core rule triggers Lambda: Vision Metadata Processor
3. Lambda validates metadata schema, enriches with store timezone and zone labels
4. Writes to DynamoDB ZoneMetrics table
5. Archives to S3 vision-meta partition

---

#### 2.3 Tertiary Ingestion: Context Signals (Fully Automatic)

| Signal | Source | Ingestion Method | Frequency |
| --- | --- | --- | --- |
| Day, week, month, season | System clock | Computed at query time | Real-time |
| Indian festivals and holidays | Pre-loaded calendar (all religions, regional variations) | Static dataset, updated annually | Annual |
| Weather | OpenWeatherMap free API | Lambda cron job, per-city | Every 6 hours |
| School and college schedules | Regional calendar dataset | Static dataset | Annual |
| Local events | Manual tag during onboarding; auto-suggested later | Semi-automatic | As needed |

**Cost:** Near zero. Public APIs plus static datasets.

---

#### 2.4 Fallback Ingestion: Manual Entry and CSV Upload

Available ONLY when automatic POS integration is not possible. Clearly labeled in UI as "Manual Mode" with a prompt to connect a billing system instead.

- Simple mobile form: item name, quantity, price (daily summary entry)
- CSV or Excel upload with downloadable template
- Validation engine checks schema, flags missing fields, suggests corrections
- Error messages displayed in retailer's selected language

---

### 3. Data Storage Layer

**Purpose:** Store all ingested, processed, and output data in a cost-efficient, queryable, and scalable manner.

---

#### 3.1 DynamoDB Tables

| Table | Partition Key | Sort Key | Purpose |
| --- | --- | --- | --- |
| Stores | store_id | — | Store profiles, tier, POS type, camera config, zone map |
| Users | user_id | — | User profiles, language preference, notification settings, role |
| Transactions | store_id | timestamp#sku_id | Normalized sales records from billing |
| SKUs | store_id | sku_id | Product catalog with metadata, category, current price |
| ZoneMetrics | store_id#zone_id | window_start | Vision metadata per zone per time window |
| Forecasts | store_id#sku_id | forecast_date | Generated demand forecasts with confidence intervals |
| PricingRecs | store_id#sku_id | rec_date | Pricing recommendations with expected impact |
| FusionScores | store_id#zone_id | score_date | Zone conversion rates, attention-to-purchase ratios |
| Alerts | store_id | alert_timestamp | Active and historical alerts with status and actions |
| CustomerSegments | store_id | segment_id | Behavioral segments derived from transaction patterns |
| ModelMetrics | model_id | eval_date | Model performance tracking per store and SKU |

**DynamoDB Configuration:**

- On-demand capacity mode (scales automatically, pay-per-request)
- Point-in-time recovery enabled
- TTL on Alerts table (90 days) and ZoneMetrics table (180 days)
- Global Secondary Index on Transactions table: category as partition key

---

#### 3.2 S3 Data Lake Structure

The data lake is organized into four top-level prefixes under the bucket ai-martintel-data. Each prefix serves a distinct purpose in the data lifecycle.

**Bucket name:** ai-martintel-data

**Top-level prefix: raw/**

This prefix stores all raw ingested data before any processing or transformation.

- **raw/transactions/** — Raw normalized transaction batches from POS systems, partitioned by store, year, month, and day. File format: Parquet.
  - Path pattern: raw/transactions/STORE_ID/YYYY/MM/DD/batch.parquet

- **raw/vision-meta/** — Raw vision metadata archives from edge devices, partitioned by store, year, month, and day. File format: Parquet.
  - Path pattern: raw/vision-meta/STORE_ID/YYYY/MM/DD/batch.parquet

- **raw/context/** — Static and periodically updated context datasets.
  - raw/context/festivals.json — Indian festival calendar covering all religions and regional variations.
  - raw/context/weather/CITY/YYYY/MM/DD.json — Daily weather snapshots per city.
  - raw/context/school-schedules/STATE.json — School and college schedule data per state.

**Top-level prefix: processed/**

This prefix stores data that has been cleaned, transformed, and prepared for model consumption.

- **processed/training-data/** — Prepared training datasets for each model type, versioned.
  - processed/training-data/demand/VERSION/ — Demand forecasting training data.
  - processed/training-data/pricing/VERSION/ — Pricing model training data.
  - processed/training-data/segmentation/VERSION/ — Customer segmentation training data.

- **processed/feature-store/** — Pre-computed feature sets per store for model inference.
  - Path pattern: processed/feature-store/STORE_ID/features_DATE.parquet

- **processed/fusion/** — Pre-computed fusion correlation data linking zone traffic to sales.
  - Path pattern: processed/fusion/STORE_ID/zone_sales_correlation_DATE.parquet

**Top-level prefix: models/**

This prefix stores trained model artifacts, evaluation metrics, and edge device model binaries.

- **models/demand/VERSION/** — Demand forecasting model artifacts.
  - models/demand/VERSION/model.tar.gz — Serialized model file.
  - models/demand/VERSION/metrics.json — Evaluation metrics (MAE, RMSE, MAPE).

- **models/pricing/VERSION/** — Pricing optimization model artifacts.

- **models/segmentation/VERSION/** — Customer segmentation model artifacts.

- **models/vision/** — Edge device computer vision model binary.
  - models/vision/yolov8n_quantized.onnx — Quantized YOLOv8-nano model for edge deployment.

**Top-level prefix: exports/**

This prefix stores generated reports and exportable summaries for retailers.

- **exports/reports/** — Weekly and monthly summary reports per store.
  - Path pattern: exports/reports/STORE_ID/YYYY/MM/weekly_summary.json

**S3 Configuration:**

- S3 Intelligent-Tiering enabled for automatic cost optimization across access frequency tiers
- Server-side encryption using SSE-S3 applied to all objects
- Lifecycle rules configured: raw data transitions to S3 Glacier storage class after 12 months
- Bucket policy restricts all access to application IAM roles only; no public access permitted


---

##Intelligence Layer (All AI/ML Models + Fusion Engine + NLG)

### 4. Intelligence Layer (AI/ML Models)

**Purpose:** Generate predictions, recommendations, and insights from ingested data using machine learning and AI.

#### 4.1 Demand Forecasting Engine (SageMaker)

**Model Architecture:**

| Attribute | v1 (MVP) | v2 (Scale) |
|---|---|---|
| Algorithm | LightGBM | DeepAR |
| Training data | Per-store transaction history | Cross-store transfer learning |
| Input features | Historical daily sales, day-of-week, festival flags, weather, zone foot traffic (from vision) | All v1 plus cross-store patterns, promotional events |
| Output | 7-day, 14-day, 30-day forecast per SKU with confidence intervals | Same plus probabilistic demand distributions |
| Granularity | Per store, per SKU | Per store, per SKU, per zone |
| Retraining | Weekly (automated SageMaker pipeline) | Weekly plus triggered on greater than 10% accuracy degradation |

**Critical design decision:** Zone foot traffic from the Vision Intelligence Engine is used as an input feature for demand forecasting. If foot traffic in a product's zone is declining, the model can predict declining demand even before sales data reflects it. This is a unique predictive signal no billing-only system has.

**Training Pipeline (Step Functions):**

1. **Trigger**: Weekly schedule or accuracy degradation event
2. **Step 1 — Data Preparation (Lambda)**: Pull last 12 months of transaction data from S3, zone metrics from DynamoDB, context signals. Join and create feature matrix.
3. **Step 2 — Feature Engineering (SageMaker Processing Job)**: Compute rolling averages, seasonal decomposition, encode categorical features, create lag features (7d, 14d, 30d), add zone traffic features per product category.
4. **Step 3 — Model Training (SageMaker Training Job)**: Train LightGBM or DeepAR model with Bayesian hyperparameter optimization.
5. **Step 4 — Model Evaluation (Lambda)**: Evaluate on hold-out set (last 30 days), compute MAE, RMSE, MAPE, compare against current production model.
6. **Step 5 — A/B Decision (Lambda)**: If new model improves accuracy by 2% or more, promote. If not, retain current model, log metrics.
7. **Step 6 — Model Deployment (SageMaker)**: Deploy to real-time endpoint, update model registry with version metadata.
8. **Step 7 — Batch Forecast Generation (SageMaker Batch Transform)**: Generate forecasts for all SKUs across all stores, write results to DynamoDB Forecasts table, trigger alert evaluation.

**Cold Start Handling:**

| Store Age | Strategy | Expected Accuracy |
|---|---|---|
| 0 days (no data) | Synthetic baseline model trained on public Indian retail distributions | 55–65% |
| 0–4 weeks | Cluster-based predictions from similar stores (same city, category mix, store size) | 65–75% |
| 4–6 weeks | Hybrid: cluster model plus emerging store-specific patterns | 75–80% |
| 6+ weeks | Fully store-specific model | 80–90% |

Retailer is informed during this period: "Predictions improve every week. High accuracy expected by week 6."

#### 4.2 Pricing Optimization Engine (SageMaker)

**Model Architecture:**

- Price elasticity estimation using gradient boosted regression
- Inputs: historical price points, corresponding sales volumes, demand forecasts, inventory age, zone conversion rate from fusion (if product has high attention but low conversion, price may be the barrier)
- Outputs: recommended price, expected revenue impact, expected volume change, confidence score

**Pricing Strategy Logic:**

| Scenario | Strategy | Trigger |
|---|---|---|
| High demand predicted (festival/season) | Suggest price increase within elasticity bounds | Forecast shows greater than 30% demand increase |
| Aging inventory (greater than 60 days) | Suggest clearance discount with expected timeline | Inventory age exceeds threshold |
| High attention, low conversion (fusion signal) | Suggest price reduction as conversion experiment | Fusion Engine flags zone |
| Competitor price significantly lower | Suggest competitive adjustment | Manual input or future scraping |

#### 4.3 Consumer Segmentation Module (SageMaker)

**Segmentation Model:**

- Algorithm: K-means clustering on transaction-derived behavioral features
- Features: purchase frequency, average basket size, category distribution, price sensitivity index, time-of-day preference, day-of-week preference
- Output: 4–6 segments with descriptive labels and characteristics
- Retraining: Monthly

**Important:** Segmentation uses transaction patterns only — no personal identity, no demographic data, no PII.

**Affinity Analysis:**

- Algorithm: FP-Growth (faster than Apriori for Indian retail basket sizes)
- Identifies frequently co-purchased items
- Output: product pair and triple affinities with support and confidence scores
- Used for: cross-placement recommendations, bundle suggestions

**Trend Detection:**

- Algorithm: Isolation Forest for anomaly detection on daily sales and zone traffic time series
- Detects: sudden demand spikes, unexpected drops, unusual zone traffic patterns
- Triggers: real-time alert via EventBridge to Lambda to SNS

#### 4.4 Fusion Engine (Core Differentiator)

**Purpose:** This is the component that makes AI-MartIntel fundamentally different from any billing-only or vision-only system. It connects what people bought with where they went and how long they stayed.

**Inputs:**

| Source | Data Used |
|---|---|
| Transactions Table (DynamoDB) | What was bought: SKU, category, quantity, price, timestamp |
| ZoneMetrics Table (DynamoDB) | What was observed: zone footfall, dwell time, occupancy |
| Zone-Product Map | Maps each product category to the physical zone(s) where it is placed (configured during onboarding, updated when layout changes) |

**Fusion Computations (per zone, per day):**

| Metric | Formula | What It Reveals |
|---|---|---|
| Zone Conversion Rate | units sold from zone / persons who visited zone | How effectively a zone converts visitors to buyers |
| Attention-to-Purchase Ratio | (traffic in zone multiplied by dwell time) / units sold | Whether products attract attention but fail to sell |
| Zone Revenue Per Visitor | revenue from zone / persons who visited zone | Monetary value of each visitor to a zone |

**Insight Generation Rules (v1 — rule-based):**

| Condition | Insight Generated |
|---|---|
| High traffic AND low conversion (top 25% traffic, bottom 25% conversion) | "High attention, low sales — investigate pricing, visibility, or product-market fit" |
| Low traffic AND high conversion (bottom 25% traffic, top 25% conversion) | "Move high-potential products to a higher-traffic zone for greater exposure" |
| Layout change detected | Compare 7-day before vs. after metrics and report impact |

**v2 Enhancement:** ML-based causal inference models that predict the sales impact of moving a product from Zone A to Zone B, accounting for cannibalization effects and traffic redistribution.

**Execution:** Fusion runs as a daily batch job (Lambda triggered by EventBridge at 5:30 AM IST). Results written to FusionScores DynamoDB table and fed to Bedrock for natural language generation.

#### 4.5 Natural Language Generation (Amazon Bedrock)

**Purpose:** Convert all model outputs — numbers, scores, predictions — into specific, actionable, plain-language recommendations in the retailer's chosen language.

**Processing Flow:**

1. **Input**: Structured JSON from any model (forecast, pricing, fusion, trend detection)
2. **Prompt Template Selection**: Template library with per-insight-type prompts. Each template includes system prompt (role, tone, length constraints), context injection slots, language instruction, and tier-appropriate complexity
3. **Bedrock API Call**: Claude 3 Haiku for speed on simple insights, Sonnet for complex weekly summaries
4. **Output**: Generated insight in vernacular language, 1–3 sentences
5. **Cache**: Stored in DynamoDB for serving; common patterns cached to reduce API calls

**Example input:**

```text
{
  "type": "restock_alert",
  "sku": "Tata Salt 1kg",
  "current_stock": 12,
  "forecast_7d": 45,
  "zone": "A3",
  "zone_traffic_daily": 210,
  "zone_conversion": 0.03,
  "language": "hi"
}
```

**Example output (Hindi):**

> टाटा नमक 1kg इस हफ्ते 45 यूनिट बिकने का अनुमान है, लेकिन आपके पास सिर्फ 12 बचे हैं। बुधवार तक रीस्टॉक करें। शेल्फ A3 पर 210 लोग रोज़ आते हैं लेकिन सिर्फ 3% खरीदते हैं — कीमत का बोर्ड ज़्यादा दिखने वाली जगह पर लगाएं।

**Prompt Engineering Approach:**

- Separate prompt templates per insight type (restock, pricing, fusion, trend, weekly summary)
- Each template tested across all 6 supported languages for quality
- Retail-specific terminology dictionary per language to avoid awkward translations
- Output length constrained: 1 to 3 sentences for alerts, 1 paragraph for weekly summaries
- Tier calibration: Lite tier gets "do this" instructions; Enterprise tier gets "do this because X data shows Y"

**Caching Strategy:** Common insight patterns cached with key: insight_type + sku_category + language + data_hash. Reduces Bedrock API calls and cost.

**Supported Languages at Launch:** Hindi, Kannada, Tamil, Telugu, Bengali, Marathi. English as default fallback.

---

##Backend Application Layer + Frontend / Presentation Layer

### 5. Backend Application Layer

**Purpose:** Business logic, API orchestration, workflow management, and event-driven processing.

#### 5.1 API Gateway Configuration

**Endpoints:**

| Method | Path | Purpose | Auth |
|---|---|---|---|
| POST | /api/v1/auth/login | User authentication | Public |
| POST | /api/v1/auth/register | New user/store registration | Public |
| GET | /api/v1/dashboard | Homepage dashboard summary | JWT |
| GET | /api/v1/forecast/{sku_id} | Forecast for specific SKU | JWT |
| GET | /api/v1/forecast/all | All SKU forecasts for store | JWT |
| GET | /api/v1/pricing/{sku_id} | Pricing recommendation | JWT |
| POST | /api/v1/pricing/accept | Accept or reject pricing rec | JWT |
| GET | /api/v1/insights/segments | Customer segments | JWT |
| GET | /api/v1/insights/affinity | Product affinity data | JWT |
| GET | /api/v1/zones/heatmap | Zone traffic heatmap | JWT |
| GET | /api/v1/zones/metrics | Zone-level metrics | JWT |
| GET | /api/v1/fusion/scores | Fusion intelligence scores | JWT |
| GET | /api/v1/fusion/recommendations | Placement recommendations | JWT |
| GET | /api/v1/alerts | Active alerts | JWT |
| PUT | /api/v1/alerts/{id}/ack | Acknowledge alert | JWT |
| GET | /api/v1/reports/weekly | Weekly summary report | JWT |
| POST | /api/v1/data/upload | CSV upload (fallback) | JWT |
| POST | /api/v1/onboarding/zones | Zone definition upload | JWT |
| GET | /api/v1/store/health | Edge device health status | JWT |

**Security Configuration:**

- JWT-based authentication using Amazon Cognito
- API key validation for POS integration endpoints
- Rate limiting: 100 requests/minute per user (API handlers), 10 requests/minute for data upload
- CORS restricted to application origins
- WAF rules on API Gateway for DDoS protection

#### 5.2 Lambda Functions

**API Handler Functions:**

| Function | Responsibility | Memory | Timeout |
|---|---|---|---|
| auth-handler | Login, registration, token refresh | 256MB | 10s |
| dashboard-handler | Aggregate homepage metrics from multiple tables | 512MB | 15s |
| forecast-handler | Retrieve forecasts, trigger on-demand if stale | 512MB | 15s |
| pricing-handler | Return pricing recs, log acceptance or rejection | 512MB | 10s |
| insights-handler | Return segments, affinity, trends | 512MB | 15s |
| zones-handler | Return heatmap data, zone metrics, dead zones | 512MB | 15s |
| fusion-handler | Return fusion scores, placement recommendations | 512MB | 15s |
| alerts-handler | Return active alerts, process acknowledgments | 256MB | 10s |
| reports-handler | Return or generate weekly summary | 1GB | 30s |
| upload-handler | Process CSV uploads (fallback mode) | 1GB | 60s |
| onboarding-handler | Process zone definitions, store setup | 512MB | 30s |

**Background Job Functions:**

| Function | Trigger | Responsibility | Memory | Timeout |
|---|---|---|---|---|
| pos-ingestion-job | EventBridge (every 15 min) | Pull transactions from POS APIs | 1GB | 5 min |
| vision-metadata-processor | IoT Core rule | Process and store edge device metadata | 512MB | 30s |
| context-enrichment-job | EventBridge (every 6 hrs) | Fetch weather, update context signals | 256MB | 60s |
| forecast-batch-job | EventBridge (daily 4 AM) | Trigger SageMaker batch forecast | 512MB | 5 min |
| pricing-batch-job | EventBridge (daily 5 AM) | Generate daily pricing recs | 512MB | 5 min |
| fusion-batch-job | EventBridge (daily 5:30 AM) | Run fusion computations | 1GB | 10 min |
| nlg-batch-job | EventBridge (daily 6 AM) | Generate NLG insights via Bedrock | 1GB | 15 min |
| alert-evaluation-job | EventBridge (daily 6:30 AM plus event-driven) | Evaluate alert conditions, send notifications | 512MB | 5 min |
| model-retraining-trigger | EventBridge (weekly Sunday 1 AM) | Trigger SageMaker training pipeline | 256MB | 60s |
| edge-health-monitor | EventBridge (every 5 min) | Check edge device heartbeats, alert on failure | 256MB | 30s |

**Runtime:** Python 3.11 for all functions. Shared Lambda layers for common utilities (DynamoDB helpers, schema validators, POS adapter library).

#### 5.3 Step Functions Workflows

**Daily Intelligence Pipeline:**

1. **Trigger**: EventBridge at 4 AM IST
2. **Parallel execution**:
   - Forecast Batch Job (SageMaker Batch Transform)
   - Fusion Batch Job (Lambda)
   - Context Update (Lambda)
3. **After all parallel steps complete**: Pricing Batch Job (Lambda plus SageMaker inference)
4. **Then**: NLG Batch Job (Lambda plus Bedrock)
5. **Then**: Alert Evaluation (Lambda)
6. **Then**: Notification Dispatch (Lambda plus SNS)
7. **End**

**Model Training Workflow:**

1. **Trigger**: Weekly Sunday 1 AM or accuracy degradation event
2. Data Preparation (Lambda)
3. Feature Engineering (SageMaker Processing Job)
4. Model Training (SageMaker Training Job)
5. Model Evaluation (Lambda)
6. Decision: If new model improves accuracy by 2% or more, deploy. Otherwise retain current model.
7. Deploy Model (SageMaker) or Log Metrics and End

#### 5.4 EventBridge Event Bus

**Custom Events:**

| Event | Source | Consumers |
|---|---|---|
| transaction.ingested | POS ingestion job | Forecast engine, alert evaluator |
| vision.metadata.received | Vision metadata processor | Fusion engine, zone analytics |
| forecast.generated | Forecast batch job | Alert evaluator, NLG generator |
| fusion.computed | Fusion batch job | NLG generator, placement recommender |
| alert.triggered | Alert evaluator | Notification dispatcher |
| model.retrained | Model training workflow | Endpoint updater, metrics logger |
| edge.heartbeat.missed | Edge health monitor | Alert evaluator (internal) |
| layout.changed | Onboarding handler (manual trigger) | Fusion engine (before/after tracking) |

---

### 6. Frontend / Presentation Layer

**Purpose:** Deliver actionable intelligence to retailers through a mobile-first, answer-first, vernacular-native interface.

#### 6.1 Technology Stack

| Component | Technology | Justification |
|---|---|---|
| Framework | React 18 + Next.js | SSR for fast initial load, excellent PWA support |
| Language | TypeScript | Type safety, better maintainability |
| Styling | Tailwind CSS | Rapid responsive design, small bundle size |
| State management | React Query (TanStack Query) | Server state caching, offline support, auto-refetch |
| Internationalization | i18next + react-i18next | Mature, supports dynamic language loading |
| Charts (Enterprise only) | Recharts (lightweight) | Only loaded for Enterprise tier desktop dashboard |
| Offline / PWA | Workbox | Service worker management, background sync |
| Notifications | Web Push API + FCM | Push notifications on mobile |

#### 6.2 Answer-First Interface Design

The UI is fundamentally different from traditional analytics dashboards. It is designed around **answers and actions**, not charts and filters.

**Homepage (All Tiers):**

```text
+-------------------------------------+
|  Store Name              [Hindi v]  |
+-------------------------------------+
|                                     |
|  WARNING: 3 Actions Needed          |
|  +-------------------------------+  |
|  | RED: Restock Tata Salt by Wed |  |
|  |   Predicted: 45 units         |  |
|  |   In stock: 12 units          |  |
|  |   [Order Now] [Dismiss]       |  |
|  +-------------------------------+  |
|  +-------------------------------+  |
|  | YELLOW: Move cooking oil to   |  |
|  |   Shelf A (near entrance)     |  |
|  |   Expected: +18% sales        |  |
|  |   [Learn More] [Dismiss]      |  |
|  +-------------------------------+  |
|  +-------------------------------+  |
|  | YELLOW: Reduce Soap X by Rs5  |  |
|  |   High attention, low sales   |  |
|  |   Expected: +12% conversion   |  |
|  |   [Apply] [Dismiss]           |  |
|  +-------------------------------+  |
|                                     |
|  Today's Summary                    |
|  +-------------------------------+  |
|  | Revenue: Rs 12,450 (+8%)      |  |
|  | Footfall: 234 customers       |  |
|  | Peak Hour: 6:30-8:00 PM       |  |
|  | Busiest Zone: Entrance        |  |
|  | Quietest Zone: Back shelf     |  |
|  +-------------------------------+  |
|                                     |
|  [Home] [Stock] [Map] [Settings]    |
+-------------------------------------+
```

**Store Map / Heatmap View (Standard + Enterprise):**

| | Zone 1 | Zone 2 | Zone 3 | Zone 4 |
| --- | --- | --- | --- | --- |
| **Status** | GREEN | YELLOW | RED | GREEN |
| **Label** | Entry | Aisle 1 | Aisle 2 | Bill Counter |
| **People** | 89 | 45 | 12 | 67 |
| **Dwell Time** | 32s | 58s | 15s | 4m wait |

**Insights displayed below the map:**

- **RED — Aisle 2 is a dead zone**
  - Only 12 visitors today
  - Average dwell: 15 seconds
  - Suggestion: Move snacks here from Aisle 1 to test traffic

- **YELLOW — Billing queue 4 min average wait**
  - Peak at 7 PM
  - Consider opening 2nd counter



#### 6.3 Tier-Specific UI Differences

| UI Element | Lite | Standard | Enterprise |
| --- | --- | --- | --- |
| Action cards (restock, price) | Yes | Yes | Yes |
| Daily summary | Basic | With zone data | Multi-store |
| Store heatmap | No | Single store | Per-store selector |
| Zone metrics detail | No | Yes | Yes |
| Fusion insights (placement recs) | No | Yes | Yes |
| Before/after layout comparison | No | No | Yes |
| Staff coverage analytics | No | No | Yes (optional) |
| Multi-store dashboard | No | No | Yes |
| Desktop dashboard with charts | No | No | Yes |
| Weekly narrative report | Yes | Yes | Yes (detailed) |
| API access | No | No | Yes |

---

#### 6.4 Responsive Design Breakpoints

| Breakpoint | Target | Priority |
| --- | --- | --- |
| 320px to 767px | Mobile (Android smartphones) | Primary — all tiers |
| 768px to 1023px | Tablet | Secondary — Standard and Enterprise |
| 1024px and above | Desktop | Enterprise tier only (full dashboard) |

---

#### 6.5 Offline Capabilities

**Service Worker Strategy (Workbox):**

| Resource Type | Strategy | Rationale |
| --- | --- | --- |
| Static assets (JS, CSS, images, icons) | Cache-first | Rarely change, load instantly |
| API responses (forecasts, alerts) | Network-first, cache fallback | Show latest data if online; cached data if offline |
| Translation files | Cache-first, background update | Language packs loaded once, updated periodically |
| Data uploads (CSV, manual entry) | Background sync | Queue locally, sync when connection restores |

**Cached Data (Available Offline):**

- Last 7 days of forecasts and recommendations
- Last 30 days of transaction summaries
- Current active alerts
- Zone metrics (last 7 days)
- User preferences and settings
- All static content and translations

**Maximum offline cache size:** 20 MB

---

#### 6.6 Notification Architecture

1. Alert triggered by Lambda alert evaluation job
2. Published to Amazon SNS Topic (per store)
3. Delivered via three channels:
   - **Push Notification** (Web Push via FCM) — appears on phone notification tray
   - **SMS** (Amazon SNS SMS) — for critical alerts (stockout, queue emergency)
   - **WhatsApp** (via WhatsApp Business API or third-party integration) — for weekly summaries and non-urgent recommendations

All notification content is in the retailer's selected language, generated by Bedrock.

---

### 7. Security Architecture

#### 7.1 Authentication and Authorization

| Component | Implementation |
| --- | --- |
| User authentication | Amazon Cognito User Pools (JWT tokens) |
| API authorization | Cognito authorizer on API Gateway |
| Role-based access | Cognito groups: store_owner, store_staff, district_manager, enterprise_admin |
| Multi-store access | District managers and enterprise admins can access multiple store_ids; enforced in Lambda authorization logic |
| Edge device auth | IoT Core X.509 certificates (per-device) |
| POS integration auth | OAuth 2.0 tokens stored in AWS Secrets Manager |

---

#### 7.2 Data Protection

| Layer | Protection |
| --- | --- |
| Data in transit | TLS 1.3 (API Gateway enforced) |
| Data at rest (DynamoDB) | AES-256 encryption (AWS-managed keys) |
| Data at rest (S3) | SSE-S3 encryption |
| Edge device local storage | AES-256 encrypted local buffer |
| Secrets (API keys, OAuth tokens) | AWS Secrets Manager with automatic rotation |
| PII | Not collected — architecture structurally prevents PII ingestion |

---

#### 7.3 Network Security

- API Gateway with AWS WAF (rate limiting, IP blocking, SQL injection prevention)
- VPC for SageMaker training and inference endpoints
- Private subnets for internal services
- Security groups restricting inbound and outbound traffic

---

#### 7.4 Audit and Compliance

- CloudTrail enabled for all API calls
- DynamoDB Streams for data change logging
- S3 access logging enabled
- Monthly automated compliance report generation
- DPDP Act compliance documentation maintained and accessible in-app

---

#### 7.5 Per-Store Data Isolation

- All DynamoDB queries include store_id as partition key — no cross-store data leakage possible
- Lambda functions validate store_id ownership against authenticated user's Cognito claims before returning data
- S3 data lake partitioned by store_id — IAM policies restrict access per partition
- Enterprise multi-store users have explicit store_id whitelists in their Cognito profile

---

### 8. Deployment Architecture

#### 8.1 Infrastructure as Code

All infrastructure defined in AWS CDK (TypeScript). The project structure is as follows:

- infrastructure/lib/api-stack.ts — API Gateway and Lambda functions
- infrastructure/lib/data-stack.ts — DynamoDB tables and S3 buckets
- infrastructure/lib/ml-stack.ts — SageMaker endpoints and training pipelines
- infrastructure/lib/edge-stack.ts — IoT Core and device management
- infrastructure/lib/frontend-stack.ts — CloudFront and S3 static hosting
- infrastructure/lib/monitoring-stack.ts — CloudWatch, alarms, and dashboards
- infrastructure/lib/security-stack.ts — Cognito, WAF, and IAM roles
- infrastructure/bin/app.ts — CDK app entry point
- infrastructure/cdk.json — CDK configuration

---

#### 8.2 CI/CD Pipeline

1. **Source Stage:** Developer pushes to GitHub, triggers AWS CodePipeline
2. **Build Stage (CodeBuild):**
   - Run unit tests
   - Run integration tests
   - Build Lambda deployment packages
   - Build frontend (Next.js build)
   - Lint and security scan
3. **Staging Stage:** Deploy to staging environment, run E2E tests and model inference smoke tests
4. **Production Stage:**
   - Blue/green deployment for Lambda
   - CloudFront invalidation for frontend
   - Canary deployment for SageMaker endpoints

---

#### 8.3 AWS Regions

| Workload | Region | Rationale |
| --- | --- | --- |
| Primary (all services) | ap-south-1 (Mumbai) | Lowest latency for Indian users |
| DR and Failover | ap-south-2 (Hyderabad) | Geographic redundancy within India |
| Edge devices | Connect to nearest region automatically | IoT Core endpoints in Mumbai |

---

#### 8.4 Scaling Configuration

| Component | Scaling Strategy |
| --- | --- |
| Lambda functions | Automatic (AWS managed), reserved concurrency for critical paths |
| DynamoDB | On-demand capacity (auto-scales with load) |
| SageMaker endpoints | Auto-scaling based on invocation rate (min 1, max 10 instances) |
| CloudFront | Global edge network (automatic) |
| API Gateway | Automatic (10,000 RPS default limit, can be increased) |
| S3 | Unlimited (automatic) |

---

### 9. Monitoring and Observability

#### 9.1 CloudWatch Dashboards

| Dashboard | Metrics Tracked |
| --- | --- |
| API Health | Request count, error rate, latency (p50, p95, p99) per endpoint |
| ML Pipeline | Model training duration, accuracy metrics, inference latency, error rate |
| Edge Fleet | Device heartbeat status, metadata ingestion rate, devices offline |
| Data Pipeline | Transactions ingested per hour, vision metadata received per hour, ingestion errors |
| Business Metrics | Active stores, daily forecasts generated, alerts sent, recommendations accepted |

---

#### 9.2 CloudWatch Alarms

| Alarm | Threshold | Action |
| --- | --- | --- |
| API error rate above 5% | 5-minute window | SNS notification to ops team (Slack and email) |
| API latency p95 above 2 seconds | 5-minute window | SNS notification to ops team |
| Edge device offline for more than 30 minutes | Per device | SNS notification to store owner plus ops team |
| Model accuracy drop above 10% | Per weekly evaluation | Trigger retraining pipeline |
| DynamoDB throttling | Any throttle event | SNS notification to ops team (evaluate capacity) |
| Lambda errors above 10 per minute | 5-minute window | SNS notification to ops team |
| Bedrock API errors above 5% | 5-minute window | Fall back to cached insights |

---

#### 9.3 Logging

- All Lambda functions log to CloudWatch Logs with structured JSON format
- Log retention: 30 days (hot), archived to S3 for 1 year
- Request tracing via AWS X-Ray for end-to-end latency analysis

---

### 10. Cost Estimation (Per 100 Stores, Monthly)

| Service | Usage Estimate | Monthly Cost (INR) |
| --- | --- | --- |
| Lambda | Approximately 5M invocations, avg 500ms, 512MB | 3,000 to 5,000 |
| API Gateway | Approximately 3M API calls | 1,000 to 2,000 |
| DynamoDB | Approximately 50M read/write units (on-demand) | 5,000 to 8,000 |
| S3 | Approximately 500 GB stored, approximately 2M requests | 2,000 to 3,000 |
| SageMaker (inference) | 1x ml.m5.large endpoint, 24/7 | 15,000 to 20,000 |
| SageMaker (training) | Weekly training jobs, approximately 4 hrs/week | 5,000 to 8,000 |
| Bedrock (Claude Haiku) | Approximately 100K insight generations | 8,000 to 12,000 |
| IoT Core | Approximately 50 edge devices, 12 msgs/hr each | 2,000 to 3,000 |
| CloudFront | Approximately 100 GB transfer | 1,000 to 1,500 |
| Cognito | Approximately 500 MAU | 0 (free tier) |
| SNS (SMS and push) | Approximately 10,000 notifications | 1,000 to 2,000 |
| CloudWatch | Logs, metrics, alarms | 2,000 to 3,000 |
| Miscellaneous | Secrets Manager, X-Ray, etc. | 1,000 to 2,000 |
| **Total (100 stores)** | | **46,000 to 71,500** |
| **Per-store cloud cost** | | **460 to 715** |

This is well within the target constraint of less than 800 INR per month per store.

**Edge device cost (one-time, per store):**

| Tier | Device | Cost (INR) |
| --- | --- | --- |
| Lite | Raspberry Pi 4 (4GB) | Approximately 5,000 |
| Standard | Raspberry Pi 4 (8GB) | Approximately 8,000 |
| Enterprise | Jetson Nano | Approximately 15,000 |

Amortized over 24 months: 208 to 625 INR per month per store.

---

### 11. Risks and Mitigations

| Risk | Severity | Likelihood | Mitigation |
| --- | --- | --- | --- |
| Edge device failure in store | Medium | Medium | Watchdog auto-restart; remote health monitoring; local data buffer survives crash; ship replacement within 48 hrs |
| POS integration breaks (API changes) | High | Medium | Adapter abstraction layer isolates changes to single module; automated integration tests catch regressions; fallback to CSV mode |
| Camera quality too low for detection | Medium | Medium | YOLOv8-nano trained on low-res Indian CCTV footage; graceful degradation (reduce accuracy, not crash); minimum camera spec documented |
| Bedrock latency or availability issues | Medium | Low | Cache common insight patterns; pre-generate daily insights in batch (not real-time); English fallback if NLG fails |
| Model accuracy poor for new store | Medium | High (expected) | Cold start strategy (synthetic to cluster to store-specific); explicit communication to retailer about accuracy timeline |
| Privacy perception issue despite no PII | High | Low | Proactive in-app privacy disclosure in vernacular; published architecture whitepaper; no face recognition is a hard technical constraint, not a toggle |
| AWS cost overrun | Medium | Low | Per-store cost monitoring; billing alerts at 80% and 100% of budget; on-demand DynamoDB prevents over-provisioning |
| Retailer stops using the product | High | Medium | Answer-first UI minimizes effort; push alerts keep value visible without login; weekly WhatsApp summaries maintain engagement |
| Data quality issues from POS | High | Medium | Validation pipeline with clear error reporting; composite key deduplication; anomaly detection on ingested data |

---

### 12. Hackathon Prototype Scope

For the AWS AI for Bharat Hackathon prototype, the following subset is built and demonstrated:

#### Built and Functional

| Component | Prototype Scope |
| --- | --- |
| Data ingestion | Simulated POS data via S3 upload plus Lambda pipeline |
| Demand forecasting | One LightGBM model on SageMaker, trained on synthetic Indian retail data |
| Vision intelligence | One camera feed processed by YOLOv8-nano on laptop (simulating edge device), zone metrics streamed to cloud |
| Fusion | Rule-based zone conversion scoring connecting sales to zone traffic |
| NLG | Bedrock-generated insights in English plus Hindi for 3 insight types (restock, placement, zone alert) |
| Dashboard | Mobile-first PWA showing action cards, basic heatmap, daily summary |
| Alerts | SNS push notification for restock alert |

#### Demonstrated but Described for Scale

- Multi-POS integration (adapter architecture shown, one adapter implemented)
- Multi-camera multi-zone processing
- Full vernacular support (6 languages — Hindi demonstrated, others described)
- Enterprise tier features (multi-store dashboard, staff analytics)
- Automated retraining pipeline (architecture described, manual trigger demonstrated)
- Edge device fleet management
