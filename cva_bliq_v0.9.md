## Historical Sovereign Default Bond Prices — Data Audit and Spreadsheet Update

I am updating an existing Excel workbook containing historical sovereign bond prices around default and restructuring events. The purpose is to build a reliable reference dataset for estimating the potential liquidation value of defaulted sovereign bonds over a short disposal horizon (approximately 30 business days).

Please help me **audit, correct and update the existing workbook**, rather than start a new research project.

### 1. Main objective

The key distinction is between:

- **Series 1 — Legacy bonds:** Prices of the original defaulted bonds before restructuring settlement. This is the primary dataset.
- **Series 2 — Restructuring package:** Market value of new securities and other consideration received at settlement, adjusted for actual exchange quantities.
- **Series 3 — Post-settlement bonds:** Prices of replacement bonds after settlement, useful for studying subsequent price changes.

These three series must not be mixed when calculating historical recovery-price statistics.

In particular, our earlier audit found that the Ukraine 2015 observations around 76–97 were for new exchange bonds, not original defaulted bonds. Please correct their classification and exclude them from Series 1 statistics.

### 2. Review the existing historical episodes

Focus on:

- Zambia 2020
- Ghana 2022
- Argentina 2020
- Ecuador 2020
- Ukraine 2024
- Greece 2012
- Ukraine 2015

Use the existing workbook's ISINs and Bloomberg identifiers as the starting point.

For each instrument, verify its identity, whether it is a legacy or replacement bond, relevant default/restructuring dates, and whether the available observations actually fall within the intended window.

**Priority:** Zambia and Ghana have relatively complete legacy-bond histories. Argentina and Ecuador need better early-default coverage. Ukraine 2024 has incomplete coverage. Greece requires careful treatment of the price source. Ukraine 2015 legacy prices may not be available from BVAL.

### 3. Reconstruct the primary legacy-bond dataset

Where available, retrieve historical Bloomberg BVAL evaluated bid and ask prices, with explicit observation dates and no forward filling.

For each legacy bond, calculate or report:

- Event-date bid, or first available bid after the event.
- Bid statistics for business days 0–5 and 0–30.
- Minimum and maximum observed bids during the 30-business-day window.
- Mean and median observed bids during that window.
- Number of valid observations and window coverage.
- Any material price changes around restructuring announcements.

Use the same explicitly documented business-day convention across episodes, and identify where it differs from the local market calendar.

Do not treat missing prices as zero. Flag stale or unusually flat observations, missing bids, anomalous spreads, and changes in pricing source.

Please distinguish BVAL evaluated bids from executable dealer quotations or actual transaction prices.

### 4. Maintain a separate restructuring reference dataset

Retain useful package and post-settlement observations, but keep them separate from legacy-bond statistics.

For restructuring packages, calculate total value per 100 original face **only where actual exchange quantities and instrument prices can be verified**. Do not use approximate allocations as confirmed package recoveries.

Record restructuring announcement dates, exchange settlement dates and the interval between the original default event and settlement.

This information will help assess how restructuring timing affects prices, but eventual package recovery must not be presented as a 30-day legacy-bond liquidation price.

### 5. Recommended spreadsheet organization

Please preserve the original raw observations and propose a clean workbook structure with:

1. **Event & Instrument Register:** Event, dates, country, original/new classification, ISIN, Bloomberg ID, legal/structural notes, data source and quality flags.
2. **Legacy Price History:** Dated observations of original defaulted bonds, with no forward filling.
3. **Legacy Event Summary:** Consistent 0–5 and 0–30 business-day statistics, observation coverage and event-level aggregation.
4. **Restructuring Reference:** Exchange terms, package values and post-settlement observations, clearly separated.
5. **Data Audit & Sources:** Corrections, exclusions, missing observations, Bloomberg queries and source references.

For event-level statistics, first aggregate the selected bonds within each event, then weight events equally. Do not weight sovereign events by the number of ISINs or daily observations.

### 6. Data quality and reporting requirements

Please explicitly identify:

- Any instruments previously misclassified as legacy bonds.
- Events where the original default date and restructuring event date differ materially.
- Observations that cannot be compared on a consistent price or recovery denominator.
- Missing or unreliable BVAL data.
- Historical periods where restructuring announcements caused significant price movements.
- Which observations are suitable for primary analysis versus supplementary context only.

Do not invent missing prices, execution costs, exchange ratios or liquidity probabilities.

If you suggest an adjustment from evaluated bids to executable block-sale prices, keep it as a **separate, clearly labeled assumption or sensitivity**, not an adjustment embedded in the historical raw data.

### 7. Deliverables

Please provide:

1. An updated Excel workbook, retaining auditable raw data.
2. A concise change log explaining corrections, exclusions and new observations.
3. A revised historical event summary based on correctly classified legacy bonds.
4. A short list of the most important unresolved data gaps, including exact Bloomberg queries or identifiers needed to close them.

**Please prioritize data integrity, correct instrument classification and comparability over expanding the number of historical episodes.** The goal is a compact, defensible historical reference dataset—not a fitted sovereign recovery model.

## Addendum — Updated Event and Instrument Metadata

An additional file is now provided:

`bliq_calibration10z_histDataSummary.csv`

Use this file as the latest event-date and instrument-classification reference, superseding earlier Bloomberg narrative summaries where they conflict.

The four previously supplied historical price CSVs remain the authoritative source for dated price observations and numerical statistics.

### Event dates and observation anchors

Distinguish three dates where applicable:

1. The sovereign default or credit-event date.
2. The observation-window anchor T0 used in the historical price data.
3. The restructuring announcement or settlement date.

Do not assume these dates coincide.

Specific treatment:

- **Ukraine 2015:** The register identifies 25 August 2015 as a distressed-price trough and 5 October 2015 as the ISDA failure-to-pay determination. Preserve this distinction. A window anchored at the observed trough must not be presented as a post-default window without qualification. Where the raw data permit, compare the two anchors as a timing sensitivity.
- **Ukraine 2024 restructuring:** The historical window is anchored to the August 2022 payment-freeze/default-related event, while the restructuring settlement occurred in August 2024. Label the episode accordingly.
- **Ecuador 2020:** The register identifies 17 April 2020 as the relevant default-related event. Preserve missing observations rather than treating later prices as event-date observations.
- **Greece 2012:** The register identifies 9 March 2012 as the PSI/CAC credit event, with only a very short surviving price history. Treat this as sparse evidence.

Use the event descriptions supplied in the register, but distinguish independently verified contractual credit events from rating actions, market troughs and research-selected anchors.

### Instrument classifications and exclusions

Use the register's Series 1, Series 2 and exclusion classifications.

The historical price files supplied for v0.9 contain confirmed Series 1 legacy-bond observations. The metadata register also contains Series 2 replacement bonds, which must not enter the primary liquidation-price statistics.

In particular, preserve the exclusion of stale Ecuador observations identified in the register.

### Price conventions and quality

The register identifies Bloomberg BVAL evaluated prices, quoted on a clean-price basis per 100 face value, with no forward filling.

Do not treat evaluated bids as executable prices. Do not automatically add accrued interest to all observations: first verify whether each instrument traded clean or flat after default and whether accrued interest was separately payable.

The suggested 2–5-point BVAL-to-execution adjustment is a modeling assumption, not an empirical observation. Keep it outside the raw historical statistics and do not apply it automatically.

### Appendix updates

Update Appendix A to incorporate the event and instrument metadata, including the chosen T0, the reason for choosing it, data coverage and any alternative event anchor.

In Appendix B.3, explicitly distinguish:

- Variation in observed prices due to default severity.
- Variation due to restructuring announcements and timing.
- Variation due to instrument characteristics and observation-window selection.
- Additional uncertainty from converting indicative bids to executable liquidation proceeds.

Do not use the historical sample to estimate French state probabilities mechanically.

Recompute all event statistics from the raw price files. Do not copy summary statistics from earlier research notes.

Preserve the overall v0.9 methodology and evaluate the proposed three-state central recovery model with exceptional non-realization treated separately.
