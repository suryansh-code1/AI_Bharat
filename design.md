# Design Document

## AI-MartIntel – AI-Driven Retail Intelligence for Bharat

### Design Overview

AI-MartIntel is a cloud-native, serverless AI platform built on AWS that delivers retail intelligence to Indian retailers through mobile-first interfaces with vernacular language support. The system leverages AWS AI/ML services (Amazon Bedrock, SageMaker) for demand forecasting, pricing optimization, and consumer insights, while maintaining cost-efficiency through serverless architecture and intelligent caching strategies.

**Core Design Principles:**
1. **Mobile-First**: Optimized for smartphone access with progressive loading
2. **Serverless Architecture**: Cost-efficient, auto-scaling using AWS Lambda and managed services
3. **AI-Powered**: Leveraging Amazon Bedrock for NLG and SageMaker for ML models
4. **Vernacular-Native**: Multi-language support baked into every component
5. **Offline-Capable**: Local caching for low-connectivity scenarios
6. **Data-Driven**: Real-time insights from transaction data with continuous model improvement

---

## High-Level Architecture

The system follows a three-tier serverless architecture:

**Tier 1 - Presentation Layer:**
- Progressive Web App (PWA) for mobile and desktop
- React-based responsive UI with offline capabilities
- Multi-language support with i18n framework
- CloudFront CDN for global content delivery

**Tier 2 - Application Layer:**
- API Gateway for RESTful endpoints
- AWS Lambda functions for business logic
- Step Functions for orchestrating ML workflows
- EventBridge for event-driven processing

**Tier 3 - Data & AI/ML Layer:**
- DynamoDB for transactional data storage
- S3 for data lake (raw transaction logs, model artifacts)
- Amazon SageMaker for ML model training and hosting
- Amazon Bedrock for natural language generation in vernacular languages
- Amazon Forecast (optional) for time-series forecasting

---

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          USER DEVICES                                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                  │
│  │   Mobile     │  │   Tablet     │  │   Desktop    │                  │
│  │  (Android/   │  │              │  │   Browser    │                  │
│  │    iOS)      │  │              │  │              │                  │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘                  │
└─────────┼──────────────────┼──────────────────┼──────────────────────────┘
          │                  │                  │
          └──────────────────┴──────────────────┘
                             │
                    ┌────────▼────────┐
                    │  Amazon         │
                    │  CloudFront     │ (CDN + Edge Caching)
                    │  (Global CDN)   │
                    └────────┬────────┘
                             │
          ┌──────────────────┴──────────────────┐
          │                                     │
┌─────────▼─────────┐              ┌───────────▼──────────┐
│   S3 Bucket       │              │   API Gateway        │
│   (Static Web     │              │   (REST APIs)        │
│    Assets)        │              │   - /forecast        │
└───────────────────┘              │   - /pricing         │
                                   │   - /insights        │
                                   │   - /data-upload     │
                                   └───────────┬──────────┘
                                               │
                    ┌──────────────────────────┴────────────────────┐
                    │                                                │
          ┌─────────▼─────────┐                          ┌──────────▼─────────┐
          │  AWS Lambda       │                          │  AWS Lambda        │
          │  (API Handlers)   │                          │  (Background Jobs) │
          │  - Forecast API   │                          │  - Data Ingestion  │
          │  - Pricing API    │                          │  - Model Training  │
          │  - Insights API   │                          │  - Alert Engine    │
          │  - User Auth      │                          └──────────┬─────────┘
          └─────────┬─────────┘                                     │
                    │                                               │
          ┌─────────┴──────────────────────────────────────────────┴─────┐
          │                                                                │
┌─────────▼─────────┐  ┌──────────────┐  ┌──────────────┐  ┌────────────▼────┐
│   DynamoDB        │  │  Amazon      │  │  Amazon      │  │  EventBridge    │
│   Tables:         │  │  SageMaker   │  │  Bedrock     │  │  (Event Bus)    │
│   - Users         │  │  - Forecast  │  │  - Claude/   │  │  - Schedule     │
│   - Transactions  │  │    Models    │  │    Titan     │  │    Triggers     │
│   - Forecasts     │  │  - Pricing   │  │  - NLG in    │  │  - Data Events  │
│   - SKUs          │  │    Models    │  │    Hindi,    │  │                 │
│   - Alerts        │  │  - Segment   │  │    Kannada   │  └─────────────────┘
└───────────────────┘  │    Models    │  │    etc.      │
                       └──────┬───────┘  └──────────────┘
                              │
                    ┌─────────▼─────────┐
                    │   Amazon S3       │
                    │   Data Lake:      │
                    │   - Raw Txn Logs  │
                    │   - Model         │
                    │     Artifacts     │
                    │   - Training Data │
                    └───────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                    EXTERNAL INTEGRATIONS                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                  │
│  │  E-commerce  │  │   Weather    │  │   SMS/       │                  │
│  │  Platforms   │  │   API        │  │   WhatsApp   │                  │
│  │  (Amazon,    │  │  (Optional)  │  │   Gateway    │                  │
│  │  Flipkart)   │  │              │  │   (SNS)      │                  │
│  └──────────────┘  └──────────────┘  └──────────────┘                  │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Component Breakdown

### 1. Data Ingestion & Storage Layer

**Purpose:** Collect, validate, and store transaction data from multiple sources

**Components:**

**1.1 Data Upload Service (Lambda)**
- Accepts CSV uploads via API Gateway
- Validates data schema and completeness
- Transforms data into standardized format
- Stores raw data in S3 and processed data in DynamoDB
- Triggers data processing pipeline via EventBridge

**1.2 E-commerce Integration Connectors (Lambda)**
- OAuth-based authentication with platforms (Amazon Seller Central, Flipkart Seller Hub)
- Scheduled data pulls (daily) via EventBridge
- Rate limiting and retry logic for API calls
- Data normalization across different platform schemas

**1.3 Data Validation Engine (Lambda)**
- Schema validation against predefined templates
- Data quality checks (missing values, outliers, duplicates)
- Generates validation reports with error details in vernacular languages
- Flags low-quality data for user review

**1.4 Storage Architecture**

**DynamoDB Tables:**
- `Users`: User profiles, preferences, language settings
- `Transactions`: Normalized transaction records (partition key: user_id, sort key: timestamp)
- `SKUs`: Product catalog with metadata
- `Forecasts`: Generated forecasts with confidence intervals
- `PricingRecommendations`: Pricing suggestions with expected impact
- `Alerts`: Active alerts and notification history
- `CustomerSegments`: Segmentation results per user

**S3 Data Lake Structure:**
```
s3://ai-martintel-data/
├── raw/
│   ├── transactions/{user_id}/{date}/
│   └── external/{source}/{date}/
├── processed/
│   ├── training-data/{model_type}/{version}/
│   └── feature-store/{user_id}/
└── models/
    ├── forecasting/{version}/
    ├── pricing/{version}/
    └── segmentation/{version}/
```


### 2. AI/ML Models Layer

**Purpose:** Generate forecasts, pricing recommendations, and consumer insights using machine learning

**Components:**

**2.1 Demand Forecasting Engine (SageMaker)**

**Model Architecture:**
- Time-series forecasting using DeepAR or Prophet algorithms
- Features: historical sales, day-of-week, month, festivals, weather (optional)
- Separate models per product category for better accuracy
- Ensemble approach combining multiple algorithms

**Training Pipeline:**
- Weekly retraining using Step Functions orchestration
- Training data: Last 12 months of transaction history
- Validation: Hold-out last 30 days for accuracy testing
- Hyperparameter tuning using SageMaker Automatic Model Tuning

**Inference:**
- Real-time endpoint for on-demand forecasts
- Batch transform for daily forecast generation (all SKUs)
- Output: 7-day, 14-day, 30-day forecasts with confidence intervals

**2.2 Pricing Optimization Engine (SageMaker)**

**Model Architecture:**
- Price elasticity estimation using regression models
- Reinforcement learning for dynamic pricing strategies
- Considers: demand forecast, current inventory, competitor prices, margins

**Pricing Strategies:**
- **Demand-based**: Increase prices during high demand periods
- **Clearance**: Aggressive discounts for aging inventory
- **Competitive**: Match or undercut competitor pricing
- **Margin-optimization**: Maximize profit within elasticity bounds

**Inference:**
- Daily batch recommendations for all SKUs
- Real-time API for what-if scenario analysis
- Output: Recommended price, expected revenue, confidence score

**2.3 Consumer Insights Module (SageMaker)**

**Segmentation Model:**
- K-means clustering on customer purchase behavior
- Features: purchase frequency, average basket size, category preferences, price sensitivity
- Output: 4-6 customer segments with descriptive labels

**Affinity Analysis:**
- Association rule mining (Apriori algorithm) for product relationships
- Identifies frequently co-purchased items
- Output: Product bundles and cross-sell recommendations

**Trend Detection:**
- Anomaly detection using Isolation Forest
- Identifies sudden demand spikes or drops
- Triggers real-time alerts via EventBridge

**2.4 Natural Language Generation (Amazon Bedrock)**

**Purpose:** Generate human-readable insights in vernacular languages

**Use Cases:**
- Convert forecast numbers into actionable text: "Stock 50 more units of Product X this week"
- Explain pricing recommendations: "Reduce price by ₹10 to clear excess inventory"
- Summarize customer segments: "Your top customers prefer premium products and shop weekly"

**Implementation:**
- Claude 3 or Titan models via Bedrock API
- Prompt engineering with language-specific templates
- Context injection with user data and business rules
- Caching for common insight patterns

**Supported Languages:**
- Hindi, Kannada, Tamil, Bengali, Gujarati
- Fallback to English if translation unavailable

---

### 3. Backend Application Layer

**Purpose:** Business logic, API orchestration, and workflow management

**Components:**

**3.1 API Gateway Configuration**

**Endpoints:**
- `POST /api/v1/auth/login` - User authentication
- `POST /api/v1/auth/register` - New user registration
- `POST /api/v1/data/upload` - CSV data upload
- `GET /api/v1/forecast/{sku_id}` - Get forecast for specific SKU
- `GET /api/v1/forecast/batch` - Get forecasts for all SKUs
- `GET /api/v1/pricing/{sku_id}` - Get pricing recommendation
- `POST /api/v1/pricing/accept` - Accept pricing recommendation
- `GET /api/v1/insights/segments` - Get customer segments
- `GET /api/v1/insights/affinity` - Get product affinity data
- `GET /api/v1/alerts` - Get active alerts
- `PUT /api/v1/alerts/{alert_id}/acknowledge` - Acknowledge alert
- `GET /api/v1/dashboard` - Get dashboard summary

**Security:**
- JWT-based authentication using AWS Cognito
- API key validation for external integrations
- Rate limiting: 100 requests/minute per user
- CORS configuration for web app origin

**3.2 Lambda Function Architecture**

**API Handler Functions:**
- `forecast-api-handler`: Retrieves forecasts from DynamoDB or triggers real-time inference
- `pricing-api-handler`: Returns pricing recommendations and tracks acceptance
- `insights-api-handler`: Aggregates and formats consumer insights
- `data-upload-handler`: Processes CSV uploads and validates data
- `dashboard-handler`: Aggregates metrics for homepage dashboard

**Background Job Functions:**
- `data-ingestion-job`: Scheduled daily data pulls from e-commerce platforms
- `forecast-batch-job`: Generates daily forecasts for all SKUs
- `pricing-batch-job`: Calculates daily pricing recommendations
- `model-training-job`: Triggers weekly model retraining pipeline
- `alert-engine-job`: Evaluates alert conditions and sends notifications

**Configuration:**
- Runtime: Python 3.11 or Node.js 18
- Memory: 512MB - 2GB depending on function
- Timeout: 30s for API handlers, 15 minutes for batch jobs
- Environment variables for configuration (DB names, model endpoints)

**3.3 Step Functions Workflows**

**Model Training Workflow:**
```
Start → Data Preparation → Feature Engineering → Model Training → 
Model Evaluation → Model Deployment → Update Endpoints → End
```

**Data Ingestion Workflow:**
```
Start → Fetch Data from Source → Validate Data → Transform Data → 
Store in S3 → Update DynamoDB → Trigger Forecast Job → End
```

**3.4 EventBridge Rules**

**Scheduled Events:**
- Daily 2 AM IST: Trigger data ingestion from e-commerce platforms
- Daily 4 AM IST: Run forecast batch job
- Daily 5 AM IST: Run pricing batch job
- Daily 6 AM IST: Run alert evaluation
- Weekly Sunday 1 AM IST: Trigger model retraining

**Event-Driven Triggers:**
- On data upload completion → Trigger forecast generation
- On forecast completion → Evaluate alert conditions
- On model training completion → Update inference endpoints

---

### 4. Frontend UI Layer

**Purpose:** Mobile-first, vernacular-enabled user interface

**Components:**

**4.1 Progressive Web App (PWA)**

**Technology Stack:**
- React 18 with TypeScript
- Tailwind CSS for responsive design
- React Query for data fetching and caching
- i18next for internationalization
- Recharts for data visualization
- Workbox for service worker and offline support

**Key Features:**
- Installable on mobile home screen
- Offline mode with cached data (last 7 days)
- Push notification support
- Progressive image loading
- Lazy loading for routes and components

**4.2 Page Structure**

**Homepage Dashboard:**
- Key metrics cards: Total revenue, forecast accuracy, top SKUs
- Active alerts section with priority indicators
- Quick action buttons: Upload data, view forecasts, check pricing
- Language selector in header

**Forecast Page:**
- SKU selector with search and filter
- Line chart: Historical actuals vs. forecasts
- Forecast table: 7-day, 14-day, 30-day predictions
- Confidence intervals and accuracy metrics
- Export to CSV option

**Pricing Page:**
- SKU list with current price and recommended price
- Expected revenue impact for each recommendation
- Accept/Reject buttons with feedback collection
- Pricing strategy explanation in vernacular language

**Insights Page:**
- Customer segment cards with descriptions
- Product affinity matrix or network graph
- Trend alerts and anomaly highlights
- Actionable recommendations

**Data Upload Page:**
- Drag-and-drop CSV upload
- Template download link
- Upload history and status
- Validation error display with fixes

**Settings Page:**
- Language preference selection
- Alert threshold configuration
- Notification preferences (SMS, WhatsApp, in-app)
- Account management

**4.3 Responsive Design Breakpoints**

- Mobile: 320px - 767px (primary focus)
- Tablet: 768px - 1023px
- Desktop: 1024px+

**4.4 Offline Capabilities**

**Service Worker Strategy:**
- Cache-first for static assets (JS, CSS, images)
- Network-first with cache fallback for API calls
- Background sync for data uploads when connection restored

**Cached Data:**
- Last 7 days of forecasts
- Last 30 days of transaction history
- User preferences and settings
- Static content and translations
