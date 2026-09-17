# NIG-S1-S2-data
NIG-S1-S2: A Dataset for NGX-Listed Companies on SDQ and CDQ

Bernard Asanbe (2026)

[DOI](https://doi.org/10.5281/zenodo.22062525)

Companion code repository: NIG-S1-S2: R Codes and Syntax for NGX-Listed Companies on SDQ and CDQ — [https://doi.org/10.5281/zenodo.22062784].

---

## Abstract

This repository contains the firm-year panel dataset associated with the Master's thesis: "The Impact of Sustainability Disclosure Quality on Firm Value: Evidence from NGX-Listed Companies Under the IFRS S1/S2 Reporting Framework." The dataset compiles financial statement data, market valuation inputs, and two content-analysis disclosure indices, Sustainability Disclosure Quality (SDQ) and Climate-related Disclosure Quality (CDQ), for companies listed on the Nigerian Exchange (NGX) across fiscal years 2021-2025. It provides a reproducible resource for studying the relationship between sustainability disclosure quality and firm value in an emerging market transitioning to mandatory IFRS S1/S2 reporting.

## Background & Summary

Nigeria's Financial Reporting Council has set 1 January 2028 as the mandatory implementation date for the IFRS Sustainability Disclosure Standards (IFRS S1 and IFRS S2), and NGX-listed companies are at varying stages of voluntarily aligning their reporting with these standards ahead of that date. This dataset scores each firm-year's public disclosures against a content-analysis checklist built directly from the IFRS S1 (general sustainability) and IFRS S2 (climate-specific) disclosure requirements, alongside the financial statement data needed to compute Tobin's Q and standard firm-level controls. Together with the companion code repository, this supports panel regression and group-comparison analysis of whether higher-quality sustainability disclosure is associated with higher firm value in this market.

## Methods

### Data Sources

- Financial statement figures (Total Assets, Total Liabilities, Net Income, Shares Outstanding, Share Price at year-end, Year of Incorporation, Date of NGX Listing, Auditor) from each firm's own audited annual report, sourced primarily from the NGX corporate disclosure portal (`doclib.ngxgroup.com`).
- Where a primary filing wasn't retrievable in a usable format: the firm's own investor relations website, then a reputable financial data aggregator (stockanalysis.com, drawing on S&P Global Market Intelligence) as a last resort — every such instance is flagged in the dataset's own `Notes / Data Quality Flags` column with a confidence rating.
- Sustainability and climate disclosure scores (SDQ, CDQ) from content analysis of each firm's own annual reports, sustainability reports, and integrated reports, scored against a checklist derived from IFRS S1's four pillars (Governance, Strategy, Risk Management, Metrics & Targets) and IFRS S2's climate-specific items across the same four pillars.
- NGX regulatory compliance status from NGX RegCo's official X-Compliance Report, used to identify firms with a missed regulatory filing, an active delisting process, or a free-float deficiency.

### Data Processing

- All financial figures standardized to NGN'000 regardless of the unit used in the original filing.
- Sample restricted by purposive criteria: listed on NGX, publicly available annual reports and financial statements, publicly available corporate reports sufficient to assess disclosure quality, complete financial data for Tobin's Q and the controls, and not delisted/suspended/under regulatory intervention/a closed-end fund. 611 of 730 theoretically possible firm-years (146 firms × 5 years) satisfy all five criteria.
- Firm-level ratios (Tobin's Q, Firm Size, Leverage, ROA, Market Value of Equity) computed via live Excel formulas from the raw financial inputs (see Data Description below for the exact formulas).
- SDQ and CDQ scored via a two-coder-plus-LLM content-analysis pass, cross-checked with a Krippendorff's alpha inter-rater reliability test (overall α = 0.810 across a 65-firm-year pilot sample).
- Two continuous control variables (Leverage, Tobin's Q) are separately winsorized at the 0.5th/99.5th percentile downstream, in the companion code repository, not in this raw dataset.

## Data Description

- **File 1: `NGX_Sustainability_Panel_Dataset_UPDATED.xlsx`**
  The master workbook. Sheets:
  - `Firm Master` — one row per firm (146 rows): sector, year of incorporation, NGX listing date, auditor, Big4 flag, NGX Compliance Status.
  - `Coding Instrument` — the SDQ/CDQ content-analysis checklist.
  - `Coding Sheet` (+ `Coding Sheet - Blind Pass 2/3/4`) — item-level disclosure scoring per firm-year, with source citations; blind passes 2-4 are the independent re-coding used for the inter-rater reliability check.
  - `Data Sources` — one row per firm: which filing the financial figures came from, with a direct link.
  - `Panel Data (Long Format)` — the full 146-firm × 5-year grid (730 possible rows), built from live formulas.
  - `Usable Sample (Sec 3.6)` — firm-level exclusions applied.
  - `Final Sample` — the analysis-ready file, 611 firm-year rows, values only: `Row_Number, Firm_ID, Ticker, Company_Name, Sector, Year, Voluntary_Adoption_Period, IFRS_S1S2_Adopter_Dummy, SDQ_Score (0-100), CDQ_Score (0-100), Shares_Outstanding, Share_Price_YearEnd, Market_Value_Equity, Total_Liabilities, Total_Assets, Net_Income, Tobin_Q, Firm_Size (LN Total Assets), Leverage, ROA, Year_Incorporation, Firm_Age, Big4_Auditor, Auditor_Name, Notes / Data Quality Flags, NGX_Compliance_Status`. This is the sheet the companion code repository's scripts and syntax read from.

  Key computed-variable formulas (all live Excel formulas in `Panel Data (Long Format)`):
  ```
  Voluntary_Adoption_Period = IF(Year >= 2023, 1, 0)
  Market_Value_Equity ('000) = Shares_Outstanding * Share_Price_YearEnd / 1000
  Tobin_Q   = (Market_Value_Equity + Total_Liabilities) / Total_Assets
  Firm_Size = LN(Total_Assets)
  Leverage  = Total_Liabilities / Total_Assets
  ROA       = Net_Income / Total_Assets
  Firm_Age  = Year - Year_Incorporation
  SDQ_Score = (sum of 20 scored IFRS S1 checklist items) / 20 * 100
  CDQ_Score = (sum of 15 scored IFRS S2 checklist items) / 15 * 100
  ```

- **File 2: `NGX_Stage1_Objective1_Descriptive_Statistics.xlsx`**
  Descriptive-statistics workbook for the disclosure-quality-level analysis: sample profile, SDQ/CDQ descriptives and normality, trend by year, sector breakdown, control-variable descriptives and correlation matrix, zero-disclosure prevalence, and the bootstrap-CI trend-figure data. Every non-Excel-native statistic (Shapiro-Wilk, the firm fixed-effects trend regression, the firm-level Kruskal-Wallis test) carries a "Method and citation" note stating which external tool computed it and how to reproduce it in SPSS.

- **File 3: `objective2_consolidated_workbook.xlsx`**
  SDQ → firm value panel regression results: model overview, fixed-effects and random-effects results, diagnostics (correlation matrix, VIF, Breusch-Pagan, Breusch-Godfrey), robustness checks, extended checks (two-way fixed effects, one-year-lagged SDQ), both figures, the full R source, and the 611-row analysis panel.

- **File 4: `objective3_consolidated_workbook.xlsx`**
  Same structure as File 3, for CDQ → firm value.

- **File 5: `objective4_consolidated_workbook.xlsx`**
  Early-adopter vs. non-adopter SDQ comparison: descriptives, normality/homogeneity tests, the primary and secondary test results, both robustness checks, the full R source, and the panel data.

- **File 6: `README.md`** (this file) — dataset structure, variable definitions, and repository usage.

## Usage Notes

This repository is data only. To run the analyses that produced Files 2-5 from File 1, or to reproduce any of the statistics in them, see the companion code repository (`NIG-S1-S2: R Codes and Syntax for NGX-Listed Companies on SDQ and CDQ`), which contains the SPSS syntax and R scripts plus step-by-step run instructions.

Files 2-5 are Excel workbooks; File 2's Excel-native cells are live formulas that recalculate if `Final Sample` in File 1 changes. Files 3-5 are static exports written by their respective R scripts — re-run the code repository's scripts to regenerate them from an updated File 1.

## Limitations

- The sample lost 119 of 730 theoretically possible firm-years (16.3%) to the purposive selection criteria; the remaining 611 are not necessarily representative of the NGX firms excluded for that reason.
- 8 of 611 firm-years (1.3%) carry a provisional SDQ/CDQ score of 0 pending Annual Report access, flagged in the `Notes / Data Quality Flags` column; a sensitivity check (documented in the companion code repository) shows every headline statistic moves by well under one percentage point when these are excluded.
- Early IFRS S1/S2 adoption is still rare in this market: only 22 of 611 firm-years are coded as early adopters, so any subgroup analysis using that flag rests on a small group.
- SDQ and CDQ reflect disclosure quality as scored against this study's own checklist derived from IFRS S1/S2; they are not an audited or regulator-certified compliance score.

## Data Availability

All files are available on Zenodo [https://doi.org/10.5281/zenodo.22062525].
GitHub repository: [https://github.com/btrex7/NIG-S1-S2-data].
Companion code repository: [https://doi.org/10.5281/zenodo.22062784].

## Citation

If using this dataset, please cite as:

Asanbe, B. (2026). *NIG-S1-S2: A Dataset for NGX-Listed Companies on SDQ and CDQ*. Zenodo. DOI: [to be inserted].

## License

Data are released under a Creative Commons Attribution 4.0 International License (CC-BY 4.0).

## Reference

Financial Reporting Council of Nigeria (FRCN). (2026). *Roadmap report for the adoption of IFRS sustainability disclosure standards in Nigeria* (Amended 2026).

International Sustainability Standards Board. (2023a). *IFRS S1 general requirements for disclosure of sustainability-related financial information*. IFRS Foundation.

International Sustainability Standards Board. (2023b). *IFRS S2 climate-related disclosures*. IFRS Foundation.

Krippendorff, K. (2018). *Content analysis: An introduction to its methodology* (4th ed.). SAGE Publications.
