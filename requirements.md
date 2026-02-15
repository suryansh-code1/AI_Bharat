

```markdown
# Requirements Document

## AI-MartIntel – AI-Driven Retail Intelligence for Bharat

**Elevator Summary:** AI-MartIntel is a zero-effort retail intelligence platform that automatically captures sales data from billing systems and analyzes in-store human movement through existing CCTV cameras using AI vision — then fuses both streams to deliver real-time, actionable insights on demand, pricing, product placement, and store layout to Indian retailers, from kirana stores to brand chains, in their own language, on their phone.

---

## Problem Statement

India's retail landscape comprises over 12 million kirana stores and countless MSMEs that collectively contribute over 80% of the country's retail market. Despite their economic significance, these retailers face two categories of blindness:

### Digital Blindness

Retailers operate with minimal technological support, relying on intuition rather than data for critical business decisions. This results in:

- **Inventory inefficiencies**: Overstocking perishables leading to 5–7% waste, or understocking high-demand items causing lost sales
- **Suboptimal pricing**: Inability to respond dynamically to market conditions, competitor pricing, or demand fluctuations
- **Limited consumer insights**: No understanding of purchasing patterns, seasonal trends, or customer segmentation
- **Revenue loss**: Estimated 15–20% revenue leakage due to poor demand planning and pricing strategies

### Physical Blindness

Retailers have zero visibility into what happens inside their own stores before a purchase occurs:

- **No knowledge of customer movement**: Which aisles draw traffic, which shelves are ignored, how long people spend in each zone
- **Wasted physical space**: Products placed in dead zones that receive no foot traffic, while high-traffic zones hold low-margin items
- **Queue and congestion ignorance**: No data on when checkout lines become long enough to drive customers away
- **Layout decisions based on distributor push, not customer behavior**: Shelf placement determined by what the distributor delivers, not by what customers actually look at and consider

### The Adoption Barrier

Every existing tool that attempts to solve either problem requires retailers to do extra work — upload files, enter data, learn dashboards, interpret charts. Indian retailers, especially kirana owners, will not do this. Adoption collapses within weeks.

Existing enterprise solutions are prohibitively expensive (₹50,000–₹5,00,000+ annually), designed for large retailers, and assume high tech literacy and reliable broadband — leaving the vast majority of Indian retailers without access to AI-powered intelligence.

**AI-MartIntel solves both blindnesses simultaneously, automatically, and affordably.**

---

## Project Objectives

1. **Zero-Effort Intelligence**: Deliver retail insights that require no manual data entry, no spreadsheet uploads, and no analytical interpretation from the retailer — the system captures, processes, and concludes automatically
2. **Fuse Digital and Physical Intelligence**: Combine what sells (billing data) with what is seen, considered, and ignored (in-store movement data) to answer questions neither data source can answer alone
3. **Democratize AI for Indian Retail**: Make AI-driven retail intelligence accessible and affordable to kirana stores, MSMEs, supermarkets, and brand stores
4. **Improve Operational Efficiency**: Reduce inventory waste by 30–40%, increase inventory turnover by 25%, and improve shelf space utilization by 20–35%
5. **Boost Revenue**: Enable 10–25% revenue uplift through optimized pricing, demand-aligned inventory, and data-driven product placement
6. **Deliver Answers, Not Analytics**: Output must be specific, actionable recommendations in plain language — not charts, not dashboards requiring interpretation
7. **Support Digital Bharat**: Build mobile-first, vernacular-language solutions that work in low-bandwidth environments across tier-1 to tier-3 cities
8. **Leverage AWS AI/ML**: Demonstrate scalable, cost-effective AI solutions using Amazon Bedrock, SageMaker, Rekognition, Kinesis, and serverless architecture
9. **Protect Privacy by Architecture**: Ensure no personally identifiable information, no facial recognition, and no stored video — privacy guaranteed by system design, not just policy

---

## Glossary

| Term | Definition |
|---|---|
| **AI_MartIntel_System** | The complete AI-driven retail intelligence platform |
| **Demand_Forecasting_Engine** | ML model that predicts future product demand based on historical sales, context signals, and zone traffic |
| **Pricing_Optimizer** | AI component that recommends optimal pricing strategies based on demand, elasticity, and conversion data |
| **Consumer_Insights_Module** | Analytics engine that segments customers and identifies behavioral patterns |
| **Vision_Intelligence_Engine** | AI computer vision system that processes CCTV feeds to extract anonymized movement, dwell time, and crowd data |
| **Fusion_Engine** | Core component that correlates digital sales data with physical movement data to generate insights neither source can produce alone |
| **Edge_Device** | On-premise processing unit (Raspberry Pi, Jetson Nano, or local PC) that runs vision AI locally and sends only anonymized metadata to the cloud |
| **Zone** | A defined area within a physical store (e.g., "entrance," "aisle 1," "billing counter," "back shelf") mapped via camera overlay during onboarding |
| **Dwell_Time** | Duration a person spends within a specific zone |
| **Heatmap** | Visual representation of foot traffic density across store zones |
| **Zone_Conversion_Rate** | Ratio of purchases made from a zone to the number of people who visited that zone |
| **Attention_to_Purchase_Ratio** | Metric comparing how many people looked at a product category vs. how many purchased it |
| **Kirana_Store** | Traditional neighborhood retail store in India |
| **MSME** | Micro, Small, and Medium Enterprise |
| **SKU** | Stock Keeping Unit — individual product identifier |
| **Vernacular_UI** | User interface supporting regional Indian languages |
| **Transaction_Log** | Historical record of sales transactions |
| **Forecast_Accuracy** | Percentage measure of how closely predictions match actual demand |
| **Price_Elasticity** | Measure of demand sensitivity to price changes |
| **Footfall** | Count of unique persons entering a store or zone within a time window |
| **Dead_Zone** | A physical area in a store that receives consistently low foot traffic relative to store average |

---

## User Personas

### Persona 1: Rajesh Kumar – Kirana Store Owner

- **Location**: Tier-2 city (Jaipur, Rajasthan)
- **Age**: 42 years
- **Tech Literacy**: Basic smartphone user
- **Language**: Hindi (primary), limited English
- **Store Setup**: Uses a local billing app for GST invoices. Has one CCTV camera installed for security.
- **Pain Points**: Frequent stockouts of popular items, excess inventory of slow-moving goods, price competition from nearby stores, no idea which shelf areas customers actually visit
- **Goals**: Reduce waste, increase daily revenue, understand what customers want — without doing any extra work
- **Device**: Android smartphone with 3G/4G connectivity
- **Product Tier**: Lite

### Persona 2: Priya Sharma – MSME E-commerce Seller

- **Location**: Bangalore, Karnataka
- **Age**: 29 years
- **Business**: Sells handmade crafts and home decor on multiple platforms, also has a small physical showroom
- **Tech Literacy**: Moderate to high
- **Language**: English and Kannada
- **Store Setup**: Uses cloud-based POS for showroom billing. Has 2 cameras in the showroom.
- **Pain Points**: Difficulty predicting seasonal demand, pricing products competitively across platforms, understanding which showroom displays attract attention vs. which are ignored
- **Goals**: Optimize inventory investment, maximize profit margins, redesign showroom layout based on actual visitor behavior
- **Device**: Laptop and smartphone
- **Product Tier**: Standard

### Persona 3: Amit Patel – District Retail Manager (Supermarket Chain)

- **Location**: Surat, Gujarat
- **Age**: 35 years
- **Responsibility**: Oversees 15 supermarket outlets across district
- **Tech Literacy**: High
- **Language**: Gujarati, Hindi, English
- **Store Setup**: Each store has ERP-integrated POS and 4–8 security cameras.
- **Pain Points**: Inconsistent performance across stores, lack of centralized insights, manual reporting processes, no data on in-store customer flow across locations
- **Goals**: Identify top/bottom performing stores, standardize best practices, optimize shelf layout and product placement using traffic data, improve overall district profitability
- **Device**: Laptop with occasional mobile access
- **Product Tier**: Standard / Enterprise

### Persona 4: Meera Nair – Brand Store Operations Head

- **Location**: Mumbai, Maharashtra
- **Age**: 38 years
- **Responsibility**: Manages 25 brand-owned stores (fashion retail) across 4 cities
- **Tech Literacy**: High
- **Language**: English, Hindi, Marathi
- **Store Setup**: Each store has centralized ERP billing, 6–10 cameras, and dedicated staff.
- **Pain Points**: Planograms designed at HQ don't reflect real customer behavior per location, no visibility into staff coverage during peak hours, cannot quantify impact of visual merchandising changes
- **Goals**: Data-driven store layouts per location, staff deployment optimization based on real traffic patterns, measure ROI of merchandising investments
- **Device**: Laptop, tablet, smartphone
- **Product Tier**: Enterprise

---

## Requirements

### Requirement 1: Automatic Data Ingestion from Billing Systems

**User Story:** As a retailer, I want my sales data to be captured automatically from my existing billing system without any manual effort, so that the AI always has current data and I never need to upload anything.

#### Acceptance Criteria

1. THE AI_MartIntel_System SHALL integrate with billing systems via direct API connection, webhook listener, or lightweight desktop sync agent to automatically capture every transaction
2. WHEN a transaction is recorded in the billing system, THE AI_MartIntel_System SHALL ingest the data (SKU, quantity, price, timestamp, store ID) within 5 minutes without any manual action from the retailer
3. THE AI_MartIntel_System SHALL support integration with at least 5 Indian POS/billing platforms (e.g., PetPooja, Posist, Mswipe, BharatPe, Vyapar) and ERP systems at launch
4. THE AI_MartIntel_System SHALL support UPI-linked billing apps and extract transaction-level data where available
5. THE AI_MartIntel_System SHALL perform automatic schema normalization across all POS sources into a canonical format: `{store_id, timestamp, sku_id, sku_name, category, quantity, unit_price, total}`
6. THE AI_MartIntel_System SHALL detect and discard duplicate transactions using composite key validation (`store_id + timestamp + sku_id + total`)
7. WHEN automatic integration is not possible (no digital billing system), THE AI_MartIntel_System SHALL offer manual daily summary entry and CSV/Excel upload as a clearly labeled secondary fallback option
8. WHEN data ingestion fails or connectivity is interrupted, THE AI_MartIntel_System SHALL queue data locally and sync automatically when connection is restored

---

### Requirement 2: AI-Powered In-Store Vision Intelligence

**User Story:** As a retailer with security cameras, I want AI to analyze customer movement in my store automatically, so that I understand which areas attract attention, where people spend time, and where they don't go — without reviewing any footage myself.

#### Acceptance Criteria

1. THE Vision_Intelligence_Engine SHALL process video feeds from existing CCTV cameras (any resolution ≥720p, including IR/night mode) to detect and track anonymized human movement
2. THE Vision_Intelligence_Engine SHALL run on an Edge_Device installed on-premise (Raspberry Pi 4, Jetson Nano, or local PC) and transmit ONLY anonymized metadata to the cloud — no video frames, no images, no recordings shall be uploaded or stored
3. WHEN processing camera feeds, THE Vision_Intelligence_Engine SHALL extract the following metrics per zone per configurable time window:
   - Person count (footfall)
   - Average dwell time (seconds)
   - Maximum simultaneous occupancy
   - Queue length at designated checkout zones
4. THE AI_MartIntel_System SHALL NOT perform facial recognition, demographic estimation, individual re-identification, or any form of personal identity capture — this is a hard architectural constraint, not a configurable setting
5. WHEN a store is onboarded, THE AI_MartIntel_System SHALL provide a one-time zone definition tool that overlays a grid on a camera still image, allowing each grid cell to be labeled as a named zone (e.g., "entrance," "aisle 3," "billing counter") — this setup shall take no more than 15 minutes per camera
6. THE Vision_Intelligence_Engine SHALL generate store-level heatmaps showing foot traffic density across all zones, updated at minimum every 30 minutes
7. THE Vision_Intelligence_Engine SHALL identify and flag Dead_Zones (zones receiving less than 10% of average store traffic consistently over 7 days)
8. WHEN queue wait time at checkout zones exceeds a configurable threshold (default: 5 minutes), THE AI_MartIntel_System SHALL generate a real-time alert
9. THE Vision_Intelligence_Engine SHALL operate at 1–2 frames per second to minimize Edge_Device processing load while maintaining tracking accuracy

---

### Requirement 3: Intelligence Fusion — Digital + Physical

**User Story:** As a store owner, I want the system to connect what people buy with where they go in my store, so that I understand not just what sold but why — and what I should change about placement, pricing, or layout to sell more.

#### Acceptance Criteria

1. THE Fusion_Engine SHALL correlate sales data (SKU-level transactions from billing) with zone traffic data (footfall and dwell time from vision) by mapping product categories to the physical zones where they are placed
2. THE Fusion_Engine SHALL compute Zone_Conversion_Rate for each zone: `(units sold from zone) / (persons who visited zone)` — updated daily
3. THE Fusion_Engine SHALL compute Attention_to_Purchase_Ratio for each product category: `(foot traffic in category zone + dwell time) / (units sold)` — to identify products that attract attention but do not convert to sales
4. WHEN a zone shows high traffic but low conversion (top 25% traffic, bottom 25% conversion), THE Fusion_Engine SHALL flag it with the insight: "High attention, low sales — investigate pricing, visibility, or product-market fit"
5. WHEN a zone shows low traffic but high conversion (bottom 25% traffic, top 25% conversion), THE Fusion_Engine SHALL recommend: "Move high-potential products here to a higher-traffic zone for greater exposure"
6. THE Fusion_Engine SHALL generate product placement recommendations based on fusion analysis, specifying which product categories to move to which zones and the expected conversion impact
7. THE Fusion_Engine SHALL provide before/after comparison capability — when a retailer makes a layout change, the system shall measure and report the impact on zone traffic and zone conversion within 7 days

---

### Requirement 4: Demand Forecasting

**User Story:** As a kirana store owner, I want to receive accurate demand forecasts for my products automatically, so that I can stock the right quantities and avoid waste or stockouts without analyzing anything myself.

#### Acceptance Criteria

1. WHEN sufficient transaction data is available (≥30 days), THE Demand_Forecasting_Engine SHALL automatically generate 7-day, 14-day, and 30-day demand forecasts for each SKU — no user action required to trigger forecasts
2. THE Demand_Forecasting_Engine SHALL incorporate context signals automatically: day of week, festivals (multi-religion Indian calendar pre-loaded), weather (public API), seasonal patterns, and school/college schedules
3. WHEN zone foot traffic data is available from the Vision_Intelligence_Engine, THE Demand_Forecasting_Engine SHALL incorporate zone-level traffic trends as an additional predictor for demand in relevant product categories
4. WHEN forecast accuracy falls below 75% for an SKU, THE AI_MartIntel_System SHALL flag the SKU and adjust to category-level or cluster-level forecasting until store-specific accuracy improves
5. THE Demand_Forecasting_Engine SHALL update forecasts daily using the latest automatically ingested transaction and vision data
6. WHEN a new SKU is added with less than 30 days of history, THE Demand_Forecasting_Engine SHALL use transfer learning from similar stores (same city, same category mix, same store size) and category-level forecasts
7. WHEN a new store is onboarded with zero historical data, THE Demand_Forecasting_Engine SHALL provide cluster-based predictions using data from similar stores for the first 4–6 weeks, then transition to store-specific models

---

### Requirement 5: Dynamic Pricing Optimization

**User Story:** As a retailer, I want AI-recommended pricing strategies delivered to me automatically, so that I can maximize profit margins while remaining competitive — without doing pricing research myself.

#### Acceptance Criteria

1. WHEN product demand forecasts and historical price data are available, THE Pricing_Optimizer SHALL automatically recommend optimal price points for each SKU
2. WHEN the Fusion_Engine identifies high attention but low conversion for a product, THE Pricing_Optimizer SHALL evaluate whether price is the conversion barrier and, if so, recommend a specific price adjustment with expected impact
3. WHEN demand is forecasted to increase (festivals, seasonal spikes), THE Pricing_Optimizer SHALL suggest price increases within acceptable elasticity bounds
4. WHEN inventory is aging or overstocked, THE Pricing_Optimizer SHALL recommend promotional pricing to accelerate sales, specifying discount amount and expected clearance timeline
5. THE Pricing_Optimizer SHALL calculate and display expected revenue and margin impact for each pricing recommendation
6. WHEN a pricing recommendation is accepted by the retailer, THE AI_MartIntel_System SHALL track actual vs. predicted outcomes over the following 14 days and refine models accordingly

---

### Requirement 6: Consumer Behavior Insights

**User Story:** As a district retail manager, I want to understand customer purchasing patterns and in-store behavior across my stores, so that I can tailor inventory, layout, and marketing strategies per location.

#### Acceptance Criteria

1. WHEN transaction data contains repeat purchase patterns, THE Consumer_Insights_Module SHALL segment customers into behavioral groups (frequent buyers, seasonal shoppers, price-sensitive, bulk buyers) — using transaction patterns only, not personal identity
2. THE Consumer_Insights_Module SHALL identify product affinity relationships (items frequently bought together) and recommend cross-placement or bundling strategies
3. THE Consumer_Insights_Module SHALL correlate purchasing segments with zone behavior from the Vision_Intelligence_Engine (e.g., "price-sensitive shoppers spend 40% more time in the promotions zone")
4. WHEN a new trend is detected (sudden spike or decline in category demand, unusual zone traffic patterns), THE AI_MartIntel_System SHALL alert users within 24 hours with a plain-language explanation
5. THE Consumer_Insights_Module SHALL generate automated weekly and monthly insight reports — delivered to the retailer, not requiring the retailer to log in and discover them

---

### Requirement 7: Vernacular Language Support

**User Story:** As a Hindi-speaking kirana store owner, I want all insights, alerts, and recommendations in my language, so that I can understand and act on them without translation.

#### Acceptance Criteria

1. THE Vernacular_UI SHALL support at least 6 Indian languages at launch: Hindi, Kannada, Tamil, Telugu, Bengali, and Marathi
2. WHEN generating text-based insights and recommendations, THE AI_MartIntel_System SHALL use Amazon Bedrock to produce natural language output in the retailer's selected language — not machine-translated from English, but contextually generated
3. THE Vernacular_UI SHALL extend to all system outputs: dashboard text, alerts, recommendations, onboarding flows, and error messages
4. THE Vernacular_UI SHALL maintain retail-specific terminology and avoid literal translations that lose contextual meaning
5. WHEN language generation is unavailable for a specific edge case, THE AI_MartIntel_System SHALL display content in English with a language indicator

---

### Requirement 8: Mobile-First, Answer-First User Experience

**User Story:** As a kirana store owner using a smartphone, I want the app to show me specific actions I should take — not charts I need to interpret — so that I can improve my business in 2 minutes a day.

#### Acceptance Criteria

1. THE AI_MartIntel_System SHALL present insights as actionable statements (e.g., "Restock Tata Salt by Wednesday — predicted demand is 45 units, you have 12 left") rather than raw charts or tables
2. THE AI_MartIntel_System SHALL provide a responsive mobile web interface optimized for screens 5–6.5 inches, requiring maximum 3 taps to reach any core insight
3. WHEN network bandwidth is below 1 Mbps, THE AI_MartIntel_System SHALL load core insights within 5 seconds using progressive loading (critical alerts and recommendations first)
4. WHEN offline, THE AI_MartIntel_System SHALL cache the last 7 days of forecasts, insights, and recommendations for offline viewing
5. THE AI_MartIntel_System SHALL minimize data usage to under 5 MB per session
6. THE AI_MartIntel_System SHALL use icon-based navigation to support low-literacy users
7. FOR Enterprise tier users, THE AI_MartIntel_System SHALL additionally provide a desktop dashboard with full visualization capabilities: heatmaps, trend charts, zone comparisons, multi-store views, and drill-down by store/category/date range/SKU

---

### Requirement 9: Real-Time Alerts and Notifications

**User Story:** As a retailer, I want the system to push critical information to me without me having to open the app, so that I can act on stockout risks, demand spikes, and crowd issues immediately.

#### Acceptance Criteria

1. WHEN forecasted demand exceeds current inventory such that stockout is predicted within 3 days, THE AI_MartIntel_System SHALL send a restock alert automatically
2. WHEN a demand spike is detected (>50% increase from baseline), THE AI_MartIntel_System SHALL notify the user within 2 hours
3. WHEN queue wait time at checkout exceeds the configured threshold, THE AI_MartIntel_System SHALL send a real-time staffing alert
4. WHEN the Vision_Intelligence_Engine detects unusual crowd density in a zone (>2x normal for that time window), THE AI_MartIntel_System SHALL alert the user
5. WHEN the Fusion_Engine identifies a significant change in zone conversion rate (>20% shift sustained over 3 days), THE AI_MartIntel_System SHALL notify the user with a recommended action
6. THE AI_MartIntel_System SHALL support alert delivery via push notification, SMS, and WhatsApp
7. THE AI_MartIntel_System SHALL allow users to configure alert sensitivity thresholds and notification channel preferences
8. ALL alerts SHALL include a specific recommended action, not just a notification of the event

---

### Requirement 10: Tiered Product Capability

**User Story:** As the product team, we need the system to serve kirana stores, supermarkets, and brand chains with appropriate capability levels, so that each segment gets relevant value at a viable price point.

#### Acceptance Criteria

1. THE AI_MartIntel_System SHALL support three product tiers:

   **Lite Tier (Kirana Stores):**
   - Automatic billing integration OR manual fallback
   - Demand forecasting and restock alerts
   - Single camera support (optional): footfall count, peak hour detection, basic shelf popularity
   - Vernacular language interface
   - Mobile-only experience

   **Standard Tier (Supermarkets, Mid-Size Retail):**
   - All Lite capabilities
   - 2–4 camera support with full zone tracking
   - Heatmaps and dwell time analytics
   - Fusion intelligence (zone conversion, placement recommendations)
   - Dynamic pricing suggestions
   - Consumer behavior segmentation

   **Enterprise Tier (Brand Stores, Chains):**
   - All Standard capabilities
   - Multi-camera support (6+ cameras per store)
   - Multi-store centralized dashboard
   - Staff movement and coverage analytics (optional, configurable)
   - Product placement optimization engine
   - Before/after layout impact measurement
   - API access for integration with existing enterprise systems
   - Dedicated support SLA

2. WHEN a user upgrades from one tier to another, THE AI_MartIntel_System SHALL retain all historical data and activate additional capabilities without re-onboarding

---

### Requirement 11: Edge Processing and Privacy Architecture

**User Story:** As a store owner, I want assurance that no personal information about my customers is captured or stored, so that I can use the system without privacy concerns or legal risk.

#### Acceptance Criteria

1. ALL vision processing SHALL occur on the Edge_Device within the store premises — no raw video, images, or frames shall leave the store network
2. THE Edge_Device SHALL transmit only structured anonymized metadata to the cloud: `{store_id, zone_id, timestamp_window, person_count, avg_dwell_seconds, max_occupancy, queue_length}`
3. THE AI_MartIntel_System SHALL NOT store, process, or transmit any biometric data, facial features, demographic estimates, or individual tracking identifiers
4. THE AI_MartIntel_System SHALL provide an in-app privacy disclosure accessible to store owners that explains in plain language (and vernacular) exactly what is and is not captured
5. THE AI_MartIntel_System SHALL comply with the Digital Personal Data Protection Act (DPDP Act) of India — and the architecture shall be designed such that compliance is structural (no PII flows through the system at any point), not dependent on policy controls
6. THE Edge_Device SHALL support remote software updates and health monitoring without requiring physical access

---

### Requirement 12: Security and Data Protection

**User Story:** As an MSME retailer, I want my business data to be secure and private, so that I can trust the platform with sensitive sales information.

#### Acceptance Criteria

1. THE AI_MartIntel_System SHALL encrypt all data in transit using TLS 1.3 or higher
2. THE AI_MartIntel_System SHALL encrypt all data at rest using AES-256 encryption
3. THE AI_MartIntel_System SHALL implement role-based access control (RBAC) for multi-user and multi-store accounts
4. THE AI_MartIntel_System SHALL provide data export and deletion capabilities upon retailer request
5. THE AI_MartIntel_System SHALL maintain per-store data isolation — no retailer shall have access to another retailer's data
6. THE AI_MartIntel_System SHALL log all data access events for audit purposes

---

### Requirement 13: Model Training, Improvement, and Cold Start

**User Story:** As the AI system, I must continuously improve prediction accuracy and handle new stores with zero historical data gracefully, so that every retailer gets useful insights from day one.

#### Acceptance Criteria

1. THE Demand_Forecasting_Engine SHALL retrain models weekly using the latest transaction and vision data via automated SageMaker pipelines
2. WHEN model performance degrades by more than 10% from baseline, THE AI_MartIntel_System SHALL trigger automated retraining
3. THE AI_MartIntel_System SHALL track model performance metrics (MAE, RMSE, forecast accuracy) per store and per SKU, stored for longitudinal analysis
4. WHEN a new model version is trained, THE AI_MartIntel_System SHALL perform A/B testing against the current version before full deployment
5. THE AI_MartIntel_System SHALL maintain model versioning and support rollback to previous versions
6. FOR new stores with zero data (cold start), THE AI_MartIntel_System SHALL:
   - Use synthetic baseline models for initial predictions (target: 55–65% accuracy)
   - Apply transfer learning from stores with similar profiles (city, category, size)
   - Transition to store-specific models after 4–6 weeks of data accumulation
   - Communicate to the retailer: "Predictions improve every week. High accuracy expected by week 6."
7. FOR vision intelligence, THE AI_MartIntel_System SHALL generate useful heatmaps and footfall insights within 48 hours of camera activation — no cold start delay for physical intelligence

---

### Requirement 14: Onboarding

**User Story:** As a new retailer, I want setup to be fast and guided, so that I can start receiving insights within days, not weeks.

#### Acceptance Criteria

1. WHEN a new store is onboarded, THE AI_MartIntel_System SHALL provide a guided setup flow covering: billing system connection, language selection, camera zone definition (if applicable), and alert preferences
2. THE onboarding flow SHALL be completable within 30 minutes for Lite tier (billing only) and within 2 hours for Standard/Enterprise tier (billing + camera setup)
3. THE AI_MartIntel_System SHALL provide video tutorials in vernacular languages demonstrating onboarding steps
4. THE AI_MartIntel_System SHALL include a Bedrock-powered chatbot for answering onboarding and usage questions in the retailer's selected language
5. THE zone definition tool for camera setup SHALL use a visual drag-and-drop grid overlay on a camera still image and shall require no technical knowledge

---

### Requirement 15: Insight Generation in Natural Language

**User Story:** As any retailer, I want the system to explain its recommendations in simple, specific language I can understand and act on, so that I never have to interpret data myself.

#### Acceptance Criteria

1. THE AI_MartIntel_System SHALL use Amazon Bedrock to convert structured model outputs (forecasts, zone metrics, fusion scores) into natural-language recommendations in the retailer's selected language
2. EACH recommendation SHALL include: what to do, why, and expected impact — in one to three sentences
3. WHEN generating weekly summary reports, THE AI_MartIntel_System SHALL produce a narrative summary (not just tables) covering: top insights, actions taken and their results, upcoming predicted events, and new recommendations
4. THE natural language output SHALL be calibrated for the retailer's tier — simpler and more direct for Lite tier, more detailed and analytical for Enterprise tier

---

### Requirement 16: Performance and Scalability

**User Story:** As the platform team, I want the system to handle a growing user base and data volume without performance degradation, so that scaling does not compromise the retailer experience.

#### Acceptance Criteria

1. THE AI_MartIntel_System SHALL support at least 10,000 concurrent users without performance degradation
2. THE AI_MartIntel_System SHALL process batch demand forecasts for 1,000 SKUs within 5 minutes
3. THE AI_MartIntel_System SHALL respond to API requests with p95 latency under 500ms
4. THE AI_MartIntel_System SHALL automatically scale storage and compute resources using AWS auto-scaling based on load
5. THE AI_MartIntel_System SHALL maintain 99.5% uptime availability measured monthly
6. THE Edge_Device software SHALL operate continuously with <500MB RAM usage and recover automatically from crashes without data loss
7. THE AI_MartIntel_System SHALL support multi-tenant architecture with per-store data isolation at scale

---

## Non-Functional Requirements

### Performance

- Dashboard load time: <3 seconds on 4G, <5 seconds on 3G
- Forecast generation: <5 minutes for 1,000 SKUs
- API response time: p95 <500ms
- Vision metadata ingestion: <30 second delay from edge to cloud
- Mobile app size: <15 MB
- Edge device processing: 1–2 FPS sustained, <500MB RAM

### Usability

- Mobile-first, answer-first design optimized for 5–6.5 inch screens
- Maximum 3 taps to reach any core insight
- Icon-based navigation for low-literacy users
- Vernacular language support for 6+ Indian languages
- All outputs phrased as actions, not data

### Reliability

- System uptime: 99.5% monthly availability
- Data backup: Daily automated backups with 30-day retention
- Disaster recovery: <4 hour RTO, <1 hour RPO
- Edge device: Auto-recovery from crash, local data queuing during connectivity loss

### Scalability

- Support 10,000+ concurrent users
- Handle 1 million+ transactions per day
- Support 5,000+ simultaneous edge device connections
- Auto-scaling for compute and storage resources

### Privacy (Architectural)

- No facial recognition at any tier
- No personal identity capture or storage
- No raw video transmission from edge to cloud
- Only anonymized aggregate metadata leaves the store
- DPDP Act compliance by design, not by policy

### Security

- TLS 1.3 for data in transit
- AES-256 encryption for data at rest
- RBAC for access control
- Per-store data isolation
- Audit logging for all data access

### Compatibility

- Browser support: Chrome, Firefox, Safari (last 2 versions)
- Mobile OS: Android 8+, iOS 13+
- Network: Functional on 3G networks (minimum 512 Kbps)
- Camera: Any CCTV ≥720p, including IR/night mode
- Edge device: Raspberry Pi 4, Jetson Nano, or x86 PC with Linux

---

## Constraints and Assumptions

### Constraints

1. **Automatic-First Architecture**: Manual data entry must never be the primary workflow — it exists only as fallback for stores without any digital billing
2. **Edge Processing Mandatory**: Raw video must never leave the store premises — all vision AI runs on-premise
3. **No PII Collection**: System architecture must structurally prevent collection of personally identifiable information
4. **Budget**: Hackathon prototype must function within limited AWS credits; production architecture must keep per-store cloud cost under ₹800/month
5. **Hardware Cost**: Edge device cost must stay under ₹15,000 per store (ideally ₹5,000 for Lite tier)
6. **Connectivity**: Must function in low-bandwidth environments (3G, 512 Kbps minimum) with offline capability
7. **POS Fragmentation**: Indian billing software market is highly fragmented — integration adapter architecture must support incremental addition of POS platforms
8. **Camera Quality**: Must work with real-world Indian CCTV footage — low resolution, poor lighting, occlusion, crowded scenes

### Assumptions

1. Target stores have at least one of: digital billing system, UPI-linked billing app, or willingness to use the manual fallback
2. Stores with cameras have existing power and network (wired or WiFi) near camera locations for edge device installation
3. Retailers have access to 3G or better internet connectivity at least once daily for data sync
4. AWS services (Bedrock, SageMaker, Rekognition, Kinesis) are available in Mumbai/Hyderabad AWS regions
5. Retailers are willing to allow anonymized analysis of in-store movement (no customer consent required since no PII is collected)
6. Initial user base will skew toward semi-organized and organized retail; kirana at scale is a Phase 2 priority

---

## Success Metrics and KPIs

### Business Impact Metrics

| Metric | Target | Timeframe |
|---|---|---|
| Forecast accuracy (MAPE) | ≤20% (≥80% accuracy) | Within 6 weeks per store |
| Revenue uplift per store | 10–25% | Within 6 months |
| Inventory waste reduction (perishables) | 30–40% | Within 6 months |
| Inventory turnover improvement | +25% | Within 6 months |
| Shelf space utilization improvement | +20–35% | Within 6 months (Standard/Enterprise tiers) |
| Pricing margin improvement | 5–10% | Within 6 months |

### Adoption Metrics

| Metric | Target | Timeframe |
|---|---|---|
| Pilot-to-paid conversion | ≥40% | After 30-day pilot |
| Weekly active usage rate | ≥60% (WAU/MAU) | Ongoing |
| Feature adoption: demand forecasts | ≥70% of active users | Within 3 months |
| Feature adoption: vision insights | ≥50% of Standard/Enterprise users | Within 3 months |
| Retention after 6 months | ≥60% | Ongoing |
| Net Promoter Score | ≥40 | Quarterly measurement |

### Technical Performance Metrics

| Metric | Target |
|---|---|
| System uptime | 99.5% monthly |
| API p95 latency | <500ms |
| Forecast batch time (1,000 SKUs) | <5 minutes |
| Dashboard load time (4G) | <3 seconds |
| Edge-to-cloud metadata delay | <30 seconds |
| Edge device uptime | >99% (auto-recovery) |
| AWS cost per store/month | <₹800 |

### Model Performance Metrics

| Metric | Target |
|---|---|
| Demand forecast MAE | <15% |
| Demand forecast RMSE | <20% |
| Pricing recommendation accuracy | Actual impact within ±10% of predicted |
| Zone conversion measurement accuracy | ±5% margin |
| Person detection accuracy (vision) | ≥90% on real Indian CCTV footage |

---

## Out of Scope (for MVP / Hackathon Prototype)

- Multi-camera cross-zone path tracking (person journey mapping across cameras)
- Staff movement and productivity analytics
- Integration with supply chain or vendor management systems
- Multi-currency and international market support
- White-label solutions
- Blockchain-based supply chain transparency
- AR/VR experiences
- Social media sentiment analysis
- Real-time competitor price monitoring via web scraping
- Native mobile app (MVP is mobile web; native app is post-MVP)
- Voice-based interface
- WhatsApp bot interface
- Auto-ordering integration with distributors

---

## Post-MVP Roadmap Features

| Phase | Timeline | Features |
|---|---|---|
| v1.1 | Month 3–4 | Multi-POS integration (top 5 platforms), improved forecast accuracy, automated weekly narrative reports |
| v1.2 | Month 5–6 | Dynamic pricing engine live, 4 additional languages, offline mode enhancement |
| v2.0 | Month 7–9 | Full fusion engine, multi-camera support, cross-zone path analysis, product placement optimizer |
| v2.5 | Month 10–12 | Staff analytics (Enterprise), FMCG brand data API (anonymized), white-label option |
| v3.0 | Year 2 | Predictive layout optimizer, voice interface, WhatsApp bot, auto-ordering integration, native mobile apps |
```
