# Kununu Scraper - Company Ratings & Employer Reviews

Extract structured data from [kununu.com](https://kununu.com) — kununu.com across DACH - company ratings, recommendation rates, 13-factor individual reviews, and salary ranges. Incremental mode tracks changes across runs. Keyword, company-list, or dataset-enrichment input modes.

**[Kununu Scraper - Company Ratings & Employer Reviews on Apify →](https://apify.com/blackfalcondata/kununu-scraper?fpr=1h3gvi)**

---

## Key features



**Search with filters** — Search by keyword and location. Filter by country code, review sort order, and more.

**Multiple input modes** — keyword search or company names list or company urls list or from apify dataset. Switch modes without re-scraping.

**Detail enrichment** — Fetch full job descriptions, structured metadata for each listing.

**Incremental mode** — Only get new or changed listings since your last run. Content hash per listing — no duplicates, no re-processing.

**Compact output** — Emit core fields only (AI-agent / MCP-friendly). Keeps response size small for LLM workflows.

**Result cap** — Stop after N listings. Set to 0 for the full catalog.

**Export anywhere** — Download as JSON, CSV, or Excel. Stream via Apify API, webhooks, or integrations with Make, Zapier, Airbyte, Keboola.

**Structured data** — Every listing returns the same schema with consistent field naming. All fields always present — `null` when unavailable, never omitted.

---

## Use cases



**Data pipeline automation**
Integrate with your ETL pipeline to collect structured listings from kununu.com on a schedule. Export to CSV, JSON, or directly to your database. Use compact mode to control output size.

**Market research**
Monitor listings, track trends, and analyze market dynamics with structured, deduplicated data from kununu.com.

**Change monitoring**
Run daily or hourly in incremental mode to capture only new, updated, or expired listings. Perfect for price-tracking, churn analysis, and alerting pipelines.

**AI / LLM training data**
Structured JSON per listing is ready for RAG pipelines, embeddings, and agent workflows. Compact mode trims tokens for LLM context windows.

---

## Quick start

```json
{
  "query": "software",
  "maxResults": 50,
  "includeDetails": true
}
```

---

## Input parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `mode` | enum | `"keyword"` | Which input to use. 'keyword' paginates search results for a query. 'companyNames' looks up a list of company names. 'companyUrls' uses direct kununu URLs. 'datasetId' reads names from an existing Apify dataset. Leave blank to auto-detect from the fields you fill in. |
| `query` | string | — | Search term for keyword mode (e.g. 'software'). Required when mode=keyword. Leave blank for other modes. |
| `companyNames` | array | — | List of company names to look up on kununu. Best match from first search page is returned. |
| `companyUrls` | array | — | List of kununu company URLs (e.g. https://www.kununu.com/de/bosch-gruppe). Country code and slug are parsed from each URL — URLs can mix DE/AT/CH. Skips search and matching. Incremental mode works per-slug. |
| `datasetId` | string | — | ID of an existing Apify dataset. Actor reads company names from this dataset and enriches with kununu ratings. |
| `companyFieldName` | string | `"companyName"` | Field in the dataset that contains the company name. Default: companyName. |
| `deduplicateField` | string | — | Optional field to deduplicate by before lookup (e.g. 'companyId'). Useful when multiple jobs share the same company. |
| `countryCode` | enum | `"de"` | Kununu country: de (Germany), at (Austria), ch (Switzerland). |
| `industryId` | string | — | Filter results by industry ID. Only applies in keyword search mode. Common IDs: 1=Banking, 2=Construction, 3=Consulting, 4=Education, 5=Publishing, 6=Finance, 7=Healthcare, 8=Insurance, 9=Pharma, 10=IT/Software, 11=Engineering, 12=Automotive, 13=Chemicals, 14=Transport, 15=Retail, 16=Telecom, 17=Energy, 18=Media, 19=Public sector, 20=NGO, 43=Hotels, 44=Gastronomy. Find other IDs by filtering on kununu.com and checking the URL. |
| `maxResults` | integer | `100` | Maximum number of results to return. 0 = no limit. |
| `maxPages` | integer | `10` | Maximum number of search result pages to scrape in keyword mode. 0 = no limit. |
| `includeDetails` | boolean | `false` | Fetch the company detail page to extract recommendation rate and website. Doubles the number of requests. |
| `compact` | boolean | `false` | Return only the most important fields. Useful for AI-agent workflows. |
| `incrementalMode` | boolean | `false` | Track changes across runs. Emits changeType (NEW/UPDATED/UNCHANGED). NOT_FOUND results are cached for 30 days. |
| `stateKey` | string | — | Unique identifier for the incremental state (e.g. 'dach-companies-v1'). Required when incrementalMode is true. |
| `includeReviews` | boolean | `false` | Also scrape individual employee reviews for each matched company. Requires includeDetails=true. Each review becomes a separate dataset item with type='review'. |
| `maxReviewPages` | integer | `10` | Maximum review pages to scrape per company (10 reviews per page). 0 = no limit. Keep this conservative — popular companies can have hundreds of pages. |
| `reviewSort` | enum | `"relevance"` | Sort order for review scraping. Default is kununu's own default (relevance), which pairs reliably with pagination. |
| `includeFullSalary` | boolean | `false` | Fetch the /gehalt page to get all salary ranges (~20 job titles) instead of the top 3 shown on the detail page. Adds one extra request per company. Requires includeDetails=true. |
| `includeCulture` | boolean | `false` | Fetch the /kultur page to extract the Kulturkompass: profile-vs-industry compass score (0-200), MODERN/TRADITIONAL binary classification, 4 culture dimensions (leadership, strategic direction, work-life, working together), strength/weakness/most-voted factors, company statements, and culture-tagged review comments. Adds one extra request per company. Requires includeDetails=true. Returns null for companies with too few culture submissions. |

---

## Output fields

Every listing returns the same 36-field schema. Missing values are `null` — never omitted.

- `type`
- `companyName`
- `slug`
- `uuid`
- `portalUrl`
- `matchStatus`
- `matchConfidence`
- `queryCompanyName`
- `rating`
- `ratingRounded`
- `reviewCount`
- `recommendationRate`
- `recommendationTotalReviews`
- `recommendationRecommended`
- `recommendationNotRecommended`
- `industry`
- `location`
- `country`
- `countryCode`
- `isTopCompany`
- `isVerified`
- `profileType`
- `totalJobs`
- `website`
- `followerCount`
- `scoreBreakdown`
- `benefits`
- `salaryRanges`
- `competitors`
- `topCompanyYears`
- `salarySatisfaction`
- `kulturKompass`
- `searchQuery`
- `scrapedAt`
- `contentHash`
- `changeType`

---

## Sample output

One object per listing. Here is a real example from a production run:

```json
{
  "type": "company",
  "companyName": "SAP SE",
  "slug": "sap",
  "uuid": "a558284b-93ad-4015-a649-a1e152c479c8",
  "portalUrl": "https://www.kununu.com/de/sap",
  "matchStatus": "EXACT_MATCH",
  "matchConfidence": 1,
  "queryCompanyName": "SAP SE",
  "rating": 4.3,
  "ratingRounded": 4,
  "reviewCount": 4844,
  "recommendationRate": 78
}
```

*Truncated — full records contain 36 fields. See Output fields for the complete schema.*

**[Try Kununu Scraper - Company Ratings & Employer Reviews now — $5 free credit, no credit card →](https://apify.com/blackfalcondata/kununu-scraper?fpr=1h3gvi)**

---

## Pricing

Pay only for what you extract. No subscription required — Apify's free $5 credit covers thousands of results.

| Event | Price (USD) |
| --- | --- |
| Actor Start | $0.005 |
| Result | $0.001 |

See the [actor on Apify](https://apify.com/blackfalcondata/kununu-scraper?fpr=1h3gvi) for current pricing.

---

## FAQ

**How do I scrape kununu.com?**
Use this actor on Apify to extract structured data from kununu.com. Configure your search query and filters in the input, then click Start — no coding required.

**How do I get kununu.com data as JSON, CSV, or Excel?**
The actor writes each listing to Apify's dataset. Download as JSON, CSV, or Excel from the Console, stream via the API, or push to Make, Zapier, Airbyte, or Keboola.

**Is it legal to scrape kununu.com?**
Web scraping of publicly available data is generally legal. This actor only accesses publicly visible information. Always check kununu.com's terms of service for your specific use case.

**How much does it cost?**
Pay-per-event pricing — you only pay for listings extracted. Apify's free $5 credit is enough to run thousands of results before you pay anything.

**How does incremental mode work?**
Each listing gets a content hash. On subsequent runs, only new or changed listings are emitted — saving time, compute, and storage. Expired listings can be tracked separately.

**Do I need an API key or credentials?**
No. Just sign up for Apify, paste your input, and click Start. No credit card required.

---

## Related products by Black Falcon Data



- [StepStone Scraper](https://apify.com/blackfalcondata/stepstone-scraper?fpr=1h3gvi) — Job listings from 18 European portals
- [Indeed Job Scraper](https://apify.com/blackfalcondata/indeed-job-scraper?fpr=1h3gvi) — Indeed job listings with salary data
- [Glassdoor Job Scraper](https://apify.com/blackfalcondata/glassdoor-job-scraper?fpr=1h3gvi) — Glassdoor listings with company ratings
- [Arbeitsagentur Scraper](https://apify.com/blackfalcondata/arbeitsagentur-scraper?fpr=1h3gvi) — Germany's official job portal (1M+ listings)
- [SEEK Scraper](https://apify.com/blackfalcondata/seek-scraper?fpr=1h3gvi) — Australia & NZ's largest job board
- [Naukri Scraper](https://apify.com/blackfalcondata/naukri-scraper?fpr=1h3gvi) — India's largest job portal

---

## Getting started with Apify

New to Apify? [Create a free account with $5 credit](https://console.apify.com/sign-up?fpr=1h3gvi) — no credit card required.

1. Sign up — $5 platform credit included
2. Open [Kununu Scraper - Company Ratings & Employer Reviews](https://apify.com/blackfalcondata/kununu-scraper?fpr=1h3gvi) and configure your input
3. Click **Start** — export results as JSON, CSV, or Excel

Need more later? [See Apify pricing](https://apify.com/pricing?fpr=1h3gvi).

---

## About Black Falcon Data

Black Falcon Data builds production-grade web scrapers for job boards and marketplace data. Browse our full actor catalog at [www.blackfalcondata.com](https://www.blackfalcondata.com).

---

*Last updated: 2026 04*
