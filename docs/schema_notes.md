# Schema Notes

**Source:** Google Analytics 360 dataset from BigQuery

---

## Tables

### 1. Google Ecommerce Dataset
- Raw ecommerce data table

### 2. daily_total_visits
- Daily visit summary samples

| Field | Type | Description |
|-------|------|-------------|
| `visit_date` | DATE | Date of visits |
| `total_visits` | INTEGER | Total visit count for the day |

### 3. GA Sessions (Partitioned)

Google Analytics sessions data partitioned by year, month, or date. Each record represents a single session from a unique visitor.

#### Fields

| Field | Type | Mode | Description |
|-------|------|------|-------------|
| `visitNumber` | INTEGER | NULLABLE | Sequential visit count for this visitor |
| `visitId` | INTEGER | NULLABLE | Unique identifier for this session |
| `visitStartTime` | INTEGER | NULLABLE | Unix timestamp (seconds) when the session started |
| `date` | STRING | NULLABLE | Session date (format: YYYYMMDD) |
| `fullVisitorId` | STRING | NULLABLE | Unique visitor identifier across all sessions |
| `userId` | STRING | NULLABLE | User ID if authenticated; otherwise null |
| `channelGrouping` | STRING | NULLABLE | Traffic channel (e.g., Organic Search, Direct, Paid Search, Social, Referral, Display) |
| `socialEngagementType` | STRING | NULLABLE | Type of social engagement if applicable |
| `trafficSource` | RECORD | NULLABLE | Nested: source, medium, keyword, campaign |
| `device` | RECORD | NULLABLE | Nested: browser, OS, device category, screen resolution |
| `geoNetwork` | RECORD | NULLABLE | Nested: country, region, city, latitude, longitude |
| `totals` | RECORD | NULLABLE | Nested: sessions, users, pageviews, bounceRate, timeOnSite, transactions, transactionRevenue |
| `customDimensions` | RECORD | REPEATED | Array of custom dimension key-value pairs (index, value) |
| `hits` | RECORD | REPEATED | Array of hit records (pageviews, events, transactions) — each hit contains: hitNumber, time, type, page, eventCategory, eventAction, eventLabel, transaction data |

#### Notes

- **Primary key:** `fullVisitorId` + `visitId` (composite key for a unique session)
- **Partitioning:** Table is partitioned by `date`; always filter by date range to control query costs
- **Nested fields:** Require flattening for analysis (e.g., `device.deviceCategory`, `totals.pageviews`)
- **Anonymous tracking:** `userId` is only populated for authenticated users; use `fullVisitorId` otherwise
