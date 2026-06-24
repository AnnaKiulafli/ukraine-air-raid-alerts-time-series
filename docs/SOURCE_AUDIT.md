# Source Provenance Audit: Ukraine Air-Raid Alert Time Series

Audit date: 2026-06-24  
Repository under audit: `AnnaKiulafli/ukraine-air-raid-alerts-time-series`

## Executive recommendation

**Go/no-go: NO-GO for migrating the production pipeline today.**

The most authoritative source identified is the operational alert chain behind the official **“Повітряна тривога” / “Air Alert”** app, supported by Ukraine's Ministry of Digital Transformation and fed by regional military/civil-protection operators. Public evidence confirms that the app receives alarm information directly from regional administrations and can notify by oblast, raion, and territorial community. However, I did **not** find a publicly documented, unauthenticated historical export/API that can reproduce the project's complete 2026 raion-level historical analysis from `2026-01-01`.

The official Telegram channel (`https://t.me/air_alert_ua`) is a more direct duplicate of app notifications than the current aggregated GitHub CSV, but it is a message stream rather than a documented data API. It may be usable for independent validation if accessed through Telegram APIs, but it is not a clean replacement without a parser, message-history backfill, and extensive reconciliation.

`alerts.in.ua` has a documented token-gated API and location types covering oblast, raion, hromada, and city, but the public client documentation emphasizes active alerts rather than full historical export. It should be treated as a **secondary comparison source**, not as the primary provenance source.

## Current repository source

The production downloader currently fetches `official_data_en.csv` from `Vadimkin/ukrainian-air-raid-sirens-dataset`:

```text
https://raw.githubusercontent.com/Vadimkin/ukrainian-air-raid-sirens-dataset/main/datasets/official_data_en.csv
```

The upstream dataset README describes two sources: “official” and volunteer-collected Telegram data. It says both datasets are updated daily, all times are UTC, the official dataset starts on 2022-03-15, and raion-level alerts became prevalent in December 2025. It also documents permanent sirens omitted from the dataset. See:

- `Vadimkin/ukrainian-air-raid-sirens-dataset` README: <https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset>
- Dataset README: <https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset/blob/main/datasets/README.md>

Classification: **secondary aggregated dataset**. It is public, convenient, and historical, but it is not the original operational system.

## 1. Source hierarchy

| Rank | Source | Classification | Why |
| --- | --- | --- | --- |
| 1 | Regional military/civil-protection administrations operating the alert signal chain | Primary operational source | Regional operators declare and cancel alerts, then transmit signals through the alerting system. |
| 2 | Official “Повітряна тривога” app/feed (`com.ukrainealarm`) | Official near-primary distribution channel | Public official/regional sources state the app receives alarm data directly from regional administrations and is supported by the Ministry of Digital Transformation. |
| 3 | Official Telegram channel `@air_alert_ua` | Official secondary duplicate of app notifications | Ajax Systems announced it as a unified Telegram channel that duplicates app notifications. It receives app signals and publishes start/end messages. |
| 4 | `api.ukrainealarm.com` / app-associated API | Potential official/API access layer, but not publicly usable without key | Public wrapper/client references state the API requires an API key requested through the site; historical export is not publicly established. |
| 5 | `alerts.in.ua` | Secondary comparison source | Volunteer/independent public service with API, map, and history/statistics; explicitly not an official government/app primary source. |
| 6 | `Vadimkin/ukrainian-air-raid-sirens-dataset` | Secondary aggregated dataset | Public GitHub aggregation derived from official and volunteer sources. Current project input. |

## 2. Evidence for each classification

### Official “Повітряна тривога” application/feed

Evidence:

- Lviv Regional Military Administration states the app receives alarm information directly from regional administrations; an operator receives the signal, transmits it to a control center, and users receive start/end notifications. <https://loda.gov.ua/en/useful-info/129927?authorId=17061>
- The same page states the app was developed/launched with support from the Ministry of Digital Transformation and supports all regions plus selected districts or territorial communities. <https://loda.gov.ua/en/useful-info/129927?authorId=17061>
- Google Play describes the app as receiving alerts from the civil defense system and requiring no registration or geolocation collection. <https://play.google.com/store/apps/details?id=com.ukrainealarm&hl=en_US>
- Ajax Systems describes the app as developed by Ajax Systems with stfalcon.com and support from the Ministry of Digital Transformation. <https://ajax.systems/press-page/air-alert-third-anniversary/>

Classification: **official near-primary distribution channel**. It is not itself the administrative authority declaring alerts, but it is the official public distribution channel closest to that chain.

### Official Telegram channel duplicating app notifications

Evidence:

- Ajax Systems announced a unified Telegram channel “Повітряна тривога” that duplicates all app notifications. <https://ajax.systems/ua/blog/air-alert-telegram-channel/>
- Ukrainian technology/media coverage reported that Ajax declined to share app software/API access for security reasons and instead launched `https://t.me/air_alert_ua`, which receives signals from the app and publishes start/end messages. <https://itc.ua/ua/novini/v-zastosunku-povitryana-trivoga-zyavilosya-nalashtuvannya-guchnosti-nulovij-trafik-ta-okremij-telegram-kanal/>
- Public Telegram web previews exist for the channel family; example channel URL: <https://t.me/air_alert_ua>.

Classification: **official secondary duplicate**, because it republishes the official app notifications but is not the originating administrative system.

### Publicly documented official API used by the app

Evidence:

- A public Python client/wrapper for `api.ukrainealarm.com` states that the API returns Ukraine air raid alarm information and requires an API key requested through the API site. <https://github.com/PaulAnnekov/ukrainealarm>
- The API landing page is available at <https://api.ukrainealarm.com/>, but public browsing in this audit did not reveal complete endpoint documentation, historical-export documentation, terms, or rate limits.

Classification: **potential official/API access layer, not yet production-eligible**. It may be related to the official app ecosystem, but without a key, endpoint documentation, historical access confirmation, or terms, it cannot be accepted as a migration target.

### `alerts.in.ua`

Evidence:

- The official `alerts.in.ua` Python client says it provides real-time air-raid and threat information and requires an API token obtained by request form. It includes filters for `oblast`, `raion`, `hromada`, and `city` location types, and reports the last-updated time in Kyiv timezone. <https://github.com/alerts-ua/alerts-in-ua-py>
- Its Google Play listing describes the app as volunteer-built and includes an explicit disclaimer that it is unofficial and not affiliated with or endorsed by a Ukrainian government entity. <https://play.google.com/store/apps/details?id=org.ukrzen.alertsinua&hl=en_US>
- Public descriptions of the service say it visualizes real-time alerts and has history/statistics, but that is not equivalent to a documented complete historical data export. <https://alerts.in.ua/en>

Classification: **secondary comparison source**. Useful for validation and sanity checks, but not an authoritative replacement.

### Current CSV aggregation

Evidence:

- The upstream README states the repository contains datasets with information about air raid sirens and has two sources: official and volunteer. <https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset>
- The dataset README states the official CSV starts 2022-03-15, is updated daily, uses UTC, and mostly contains raion-level alerts since December 2025. <https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset/blob/main/datasets/README.md>

Classification: **secondary aggregated dataset**. It is the current source and best public historical source found during this audit, but it is not the origin.

## 3. Availability and coverage matrix

| Candidate | Owner/operator | Official/primary/secondary | Public documentation | Access method | Auth | Historical coverage | `2026-01-01` available? | Granularity | Start/finish timestamps | Timezone | Corrections/updates | Rate limits | License/terms | Automated downloading permitted? | Risks/limitations |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Official app/feed | Ajax Systems / stfalcon.com; supported by Ministry of Digital Transformation; operational inputs from regional administrations | Official near-primary distribution | App-store pages, regional government pages, Ajax pages; no public data-export schema found | Mobile app push/feed | No user registration for app; backend/API access not public | App UI reportedly includes alert map/history, but no public bulk historical export found | Not confirmed via public export | Oblast, raion, hromada; app pages also mention city/region selection | Yes in notifications; bulk export not documented | App-facing local time; exact API timezone not documented | Operator-issued start/end; correction semantics not documented | Not documented | App-store/platform terms; no dataset license found | Not confirmed for automated data extraction | Most authoritative, but no documented public historical API/export for reproducible research |
| Official Telegram channel `@air_alert_ua` | Ajax Systems channel duplicating app notifications | Official duplicate / secondary to app | Public channel announcement; Telegram message format informal | Telegram channel history via Telegram clients/API | Telegram API account/app credentials required for robust scraping | Channel history likely from March 2022 onward, but completeness must be tested | Likely yes if channel history retained and accessible | Message text historically region-based; raion/hromada availability after local-alert rollout must be parsed/tested | Start and finish messages exist | Telegram message timestamps are UTC internally; message text may use local time | Telegram edits/deletions possible; correction rules not documented | Telegram flood limits, undocumented for scraping at scale | Telegram ToS; channel content license not explicit | Not explicitly granted; must respect Telegram ToS and channel limits | Parsing fragility, message edits/deletions, language/text changes, no schema, no guaranteed completeness |
| `api.ukrainealarm.com` | App/API ecosystem; wrapper by third party | Potential official API access layer | Minimal public evidence; wrapper README only | HTTP API | API key required via request form | Not documented publicly | Not confirmed | Current/active alert granularity unknown without docs/key | Unknown for historical records | Unknown | Unknown | Unknown | Terms/license not found in public docs | Not confirmed | Cannot accept without key, docs, rate limits, terms, and historical endpoint confirmation |
| `alerts.in.ua` | `alerts-ua` / alerts.in.ua service operators | Secondary comparison source; unofficial | Website and official Python client | HTTP API via token/client; website history/statistics | API token by request form | Public site has history/statistics; API client docs emphasize active alerts | Not confirmed for API bulk export | Oblast, raion, hromada, city filters in client | Active alerts include start; finished history not documented in client README | Last update documented as Kyiv timezone in client | Secondary aggregation; correction behavior not documented | Not documented publicly | Client MIT license; data/API terms not fully established | Tokened API implies permitted use after approval, but bulk automated historical downloading not confirmed | Unofficial, tokened, history endpoint not established, not primary |
| Vadimkin CSV | GitHub repository maintainer | Secondary aggregated dataset | GitHub README/datasets README | Raw CSV download | None | Official CSV from 2022-03-15; daily updates | Yes, by upstream statement and current project use | Mostly raion since Dec 2025; includes oblast/raion/hromada | Yes: started/finished fields in CSV | UTC per upstream README | Upstream updates daily; correction policy not formalized | GitHub raw limits | MIT repository license | Public raw CSV download currently used | Secondary source, provenance not direct, transformation assumptions external |

## 4. Migration feasibility

### Can any candidate reproduce the complete 2026 raion-level historical analysis?

**Not proven.** No investigated authoritative/direct candidate has a publicly documented, complete, machine-readable historical export that is confirmed to contain all raion-level start and finish intervals from `2026-01-01` onward.

- The official app/feed is authoritative but not publicly exportable in documented bulk form.
- The official Telegram channel may contain the needed notifications, but this audit did not run a full Telegram history extraction and parse. It therefore cannot be claimed to match or reproduce the current CSV.
- `api.ukrainealarm.com` may provide official API access, but public documentation found during this audit does not confirm historical endpoints or rate/terms.
- `alerts.in.ua` is useful for cross-checking and may have historical views/statistics, but it is unofficial and its public Python client documentation is oriented around active alerts.

### Feasibility verdict by source

| Candidate | Migration feasibility | Verdict |
| --- | --- | --- |
| Official app/feed | Low until a documented historical export/API is obtained | Reject for immediate migration; pursue official access |
| Official Telegram channel | Medium for validation, low-to-medium for production source | Reject as sole production source until parser/backfill validation succeeds |
| `api.ukrainealarm.com` | Unknown; potentially high if official historical endpoints exist | Hold pending API key, docs, terms, and test extraction |
| `alerts.in.ua` | Medium for comparison; low as primary | Accept only as secondary validation source |
| Vadimkin CSV | High continuity for current project | Keep as current source pending validation against more authoritative sources |

## 5. Validation methodology

This is a proposed procedure. **No match claims should be made until it is actually run.**

### Sample design

Select at least seven calendar dates in `Europe/Kyiv` time, including weekdays/weekends and dates likely to include alerts. Recommended initial sample:

1. 2026-01-01
2. 2026-01-15
3. 2026-02-01
4. 2026-03-15
5. 2026-04-01
6. 2026-05-09
7. 2026-06-01

Select at least five oblasts plus Kyiv City vs Kyivska oblast:

- Kyiv City (`м. Київ`) separately from Kyivska oblast
- Kyivska oblast
- Kharkivska oblast
- Dnipropetrovska oblast
- Odeska oblast
- Lvivska oblast
- Donetska oblast, if source coverage permits

### Data extraction inputs

1. Current CSV: download the existing `official_data_en.csv` exactly as the production pipeline does.
2. Most authoritative available comparison source:
   - preferred: official app/API historical endpoint, if access is granted;
   - fallback: official Telegram channel history parsed from `@air_alert_ua`;
   - secondary check only: `alerts.in.ua` API/history if token and historical endpoint are available.

### Normalization rules

- Normalize all timestamps to timezone-aware UTC for equality tests.
- Preserve original source timestamps and timezone labels in audit output.
- Convert local-date windows using `Europe/Kyiv`, not fixed UTC offsets.
- Normalize administrative names to stable IDs where available; otherwise use a controlled mapping table for Ukrainian/English names.
- Keep Kyiv City distinct from Kyivska oblast.
- Keep source administrative level as an explicit field: `oblast`, `raion`, `hromada`, `city`, or `unknown`.

### Record-level comparison checks

For each selected date/oblast/admin unit:

1. Count start events by source.
2. Count finish events by source.
3. Compare start timestamp deltas:
   - exact UTC match;
   - within ±60 seconds;
   - within ±5 minutes;
   - no match.
4. Compare finish timestamp deltas using the same thresholds.
5. Compare interval duration deltas.
6. Detect records in CSV missing from authoritative source.
7. Detect records in authoritative source missing from CSV.
8. Detect duplicates within each source after normalization.
9. Detect overlapping intervals for the same administrative unit and alert type.
10. Identify apparent corrections:
    - Telegram edited messages;
    - source records with changed finish times across repeated pulls;
    - replacement/cancellation messages;
    - CSV rows whose intervals change between upstream commits.

### Outputs to generate in a future validation PR

- `data/diagnostics/source_validation_sample.csv` (ignored or committed only if policy allows small diagnostic fixtures)
- `reports/source_validation/summary.md`
- `reports/source_validation/mismatches.csv`
- `reports/source_validation/duplicates.csv`
- `reports/source_validation/admin_level_crosswalk.csv`

### Acceptance thresholds

A migration candidate should not be accepted unless:

- it provides documented permission for automated retrieval;
- it covers every selected test date from `2026-01-01` onward;
- it preserves Kyiv City separately from Kyivska oblast;
- it exposes start and finish timestamps;
- it includes raion-level records for the current analysis period;
- validation shows no unexplained systematic missingness or level mismatch;
- rate limits allow daily reproducible updates;
- correction/update behavior is either documented or empirically monitorable.

## 6. Recommended source

**Recommended current production source: keep `Vadimkin/ukrainian-air-raid-sirens-dataset` temporarily.**

**Recommended provenance target: official app/API access, if UkraineAlarm/API maintainers can provide documented historical endpoints and terms.**

**Recommended validation source: official Telegram `@air_alert_ua` plus `alerts.in.ua` as a secondary cross-check.**

Rationale:

- The official app/feed is closest to the operational chain, but is not currently accessible as a reproducible historical dataset.
- The current CSV is already historical, UTC-normalized, and daily updated, but remains secondary.
- The official Telegram channel is more direct than the current CSV but requires a new parser and cannot be assumed complete without a full backfill test.
- `alerts.in.ua` can help identify discrepancies, but its unofficial status prevents it from becoming the primary source by default.

## 7. Exact reasons for accepting or rejecting each candidate

### Official app/feed

Accept as: **provenance target and authority reference**.

Reject for immediate pipeline migration because:

- no public bulk historical export was found;
- no public schema was found for start/finish interval records;
- no public correction/update policy was found;
- no public rate limits or automated-use terms were found;
- no evidence from this audit confirms full `2026-01-01` raion-level historical availability via automated download.

### Official Telegram channel

Accept as: **official duplicate stream for future validation and possible backfill experiment**.

Reject for immediate production migration because:

- Telegram is not a structured data API for this domain;
- robust access requires Telegram API credentials and flood-limit handling;
- message formats may change;
- edited/deleted messages and correction semantics are not documented;
- raion/hromada message history from `2026-01-01` has not yet been parsed and compared;
- automated downloading permission is governed by Telegram ToS and channel norms, not a dataset license.

### `api.ukrainealarm.com`

Accept as: **highest-priority access request target**.

Reject for immediate production migration because:

- public documentation found during this audit only confirms API-key requirement, not historical endpoints;
- no public schema, rate limit, license, or terms were found;
- no test query could be run without a key;
- data coverage from `2026-01-01` is not confirmed.

### `alerts.in.ua`

Accept as: **secondary comparison and discrepancy detection source**.

Reject as primary source because:

- its app listing says it is unofficial and not affiliated with or endorsed by a Ukrainian government entity;
- public client documentation emphasizes active alerts rather than complete historical export;
- API access requires token approval;
- rate limits and automated historical-download terms were not established;
- data lineage and correction behavior are secondary to official channels.

### Current Vadimkin CSV

Accept for now because:

- it is public, downloadable, historical, UTC-normalized, and already integrated;
- upstream says the official dataset starts 2022-03-15 and is updated daily;
- upstream says raion-level alerts are mostly present since December 2025, matching the project's 2026 raion focus.

Reject as long-term authoritative source because:

- it is an aggregated GitHub dataset, not the operational origin;
- its correction/update policy is informal;
- omitted permanent sirens and possible transformation assumptions require downstream awareness;
- provenance depends on upstream processing outside this repository.

## 8. Estimated code changes required for a future migration

No production files were changed in this audit. If a future migration is approved, estimated changes are:

1. **Downloader abstraction**
   - Refactor `scripts/download_data.py` into source-specific fetchers.
   - Add config for source type, credentials, and cache directory.
   - Preserve existing CSV downloader as fallback.

2. **Credential handling**
   - Add `.env`/environment-variable support for API tokens or Telegram credentials.
   - Update documentation to avoid committing secrets.

3. **Source adapters**
   - API adapter for official endpoint, if access is granted.
   - Telegram adapter if channel backfill is selected.
   - Optional `alerts.in.ua` adapter for diagnostics only.

4. **Schema normalization**
   - Add source-to-canonical schema mapping.
   - Preserve original fields and raw payload hashes for auditability.
   - Add administrative-level and administrative-ID crosswalks.

5. **Correction tracking**
   - Store immutable raw pulls by date/source.
   - Add diffing between repeated pulls to detect changed start/finish times.
   - Add explicit correction logs.

6. **Validation suite**
   - Add record-level comparison checks described above.
   - Add fixture-free tests for timestamp normalization, Kyiv City/Kyivska separation, duplicate detection, and admin-level mapping.

7. **Documentation**
   - Update README only after validation is complete and the production source changes.
   - Add source terms and citation requirements.

Estimated implementation size after source access is approved: **medium** (approximately 2-5 focused PRs), with the largest unknown being Telegram/API historical extraction and administrative crosswalk quality.

## 9. Go/no-go recommendation

**NO-GO for production migration now.**

Proceed with the following next steps instead:

1. Request documented access to `api.ukrainealarm.com` or another official historical endpoint from the Air Alert/Ajax/Ministry-supported ecosystem.
2. Ask explicitly for:
   - historical records from at least `2026-01-01`;
   - raion-level coverage;
   - start and finish timestamps;
   - timezone specification;
   - correction/update semantics;
   - rate limits;
   - terms permitting automated research downloads and derived public analysis.
3. In a separate diagnostic PR, implement a non-production validation harness for the seven-date/five-oblast comparison.
4. Use `@air_alert_ua` and `alerts.in.ua` only as validation/comparison sources unless and until they pass the acceptance thresholds.
5. Keep the current CSV pipeline unchanged until validation proves a replacement can reproduce the current complete 2026 raion-level analysis.
