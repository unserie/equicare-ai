# EquiCare-AI — Algorithmic Fairness Audit Platform

> **The standard for algorithmic accountability.**
> Upload any model. Expose hidden bias. Defend every decision.

---

**Author:** Maisun Ansary |
**Course:** GHI 563: Foundations of Public Health Applications of Artificial Intelligence |
**Semester:** Spring 2026 |
**Institution:** University of Arizona |
**Submission deadline:** 5/11/2026 | 11:59 PM

---

## Project Description

EquiCare-AI is a browser-based algorithmic fairness audit platform that computes stratified bias metrics across deployed decision systems — without transmitting any data to a server. It targets researchers, compliance officers, and institutional reviewers who need to assess, document, and defend the fairness properties of classification, risk-scoring, and resource-allocation models across protected demographic strata.

The platform operates in two modes:

- **Demo mode** — five pre-loaded landmark scenarios drawn from peer-reviewed empirical findings, covering criminal justice, healthcare, hiring, financial services, and academic research
- **Custom mode** — accepts user-uploaded CSV or JSON datasets for live audit against the same pipeline

All processing occurs locally in the browser. No data is ever transmitted to any external server.

---

## Proposal Pivot & Instructor Feedback

The original proposal for this project was for a different system unrelated to algorithmic auditing. The pivot to EquiCare-AI was driven by a more pressing and tractable problem that emerged during the course — the lack of accessible, self-contained tooling for auditing deployed AI systems for demographic bias. The final prototype reflects a more focused and technically grounded direction than the original proposal would have produced.

**Key feedback received:**

- **Missing benchmark system.** The approach attempts to audit any arbitrary model in a model-agnostic way, but lacked a clear benchmark — a reference standard against which model outputs could be evaluated. Instructor noted that meaningful equity auditing requires a ground-truth comparison mechanism, similar to how capability benchmarks work in tools like Arena and LLM stats. This critique is directly acknowledged in the Known Limitations section: the current prototype renders pre-specified metric values derived from published empirical literature rather than computing them live against an uploaded model.

- **Scope too broad.** A fully model-agnostic auditing architecture — one that could handle every model type, parameter space, and deployment context — was identified as too broad of a scope.

- **User input pathway unclear.** The prototype did not make it sufficiently clear to users what inputs were required and what the system could actually do with them.

- **IP and access barriers.** The discussion raised the practical problem that auditing proprietary models (e.g. a healthcare insurer's internal XGBoost pipeline, or OpenAI's API) is blocked by IP restrictions and the absence of model access.

The prototype is submitted as a working proof-of-concept with honest disclosure of what is simulated versus what is computationally live.
---

## Problem Statement

Algorithmic decision systems deployed across high-stakes domains — criminal justice, healthcare, credit, and hiring — have been documented to produce outcomes that are systematically disparate across race, gender, and socioeconomic status. Despite this evidence, most institutions lack accessible, audit-ready tooling that can:

1. Compute multiple fairness metrics simultaneously and surface their mutual incompatibilities
2. Attribute bias to specific proxy features using SHAP values
3. Map findings to governance frameworks (Belmont Report, WHO AI Ethics, FAIR principles)
4. Generate exportable compliance-ready reports

EquiCare-AI addresses this gap with a self-contained, zero-dependency audit tool that runs entirely in the browser and produces structured, citable outputs grounded in published fairness literature.

---

## Features

### Audit Pipeline (5-stage)
| Stage | Description |
|---|---|
| 1. System characterisation | Decision task, model architecture, protected attribute configuration, deployment population |
| 2. Stratified fairness metrics | DIR, equalized odds, calibration error, stratified AUC with bootstrapped 95% CIs |
| 3. SHAP proxy detection | P(sensitive attribute \| feature) across all features via conditional independence testing |
| 4. Ethics RAG retrieval | Belmont Report, WHO AI Ethics (2021), Chouldechova impossibility, Hardt et al. |
| 5. Mitigation synthesis | Threshold optimisation, preprocessing reweighting, adversarial debiasing, post-processing calibration |

### Metrics Computed
- **Disparate Impact Ratio (DIR)** — four-fifths rule threshold ≥ 0.80
- **Equalized Odds** — simultaneous FPR and FNR parity (Hardt et al., 2016)
- **Calibration error** — cross-group maximum gap
- **Stratified AUC** — per-group with bootstrapped 95% confidence intervals
- **Individual fairness** — Lipschitz constraint operationalisation (Dwork et al., 2012)
- **SHAP-attributed proxy risk** — P(sensitive | feature) at three severity tiers

### Results Views (2-layer navigation)
- **Layer 1 — Metric-level audit:** Audit Metrics · Disparate Impact · Mitigation
- **Layer 2 — Governance-level audit:** Institutional Impact · FAIR Assessment

### Privacy & Security
- **Compliance banner** — persistent bar at the top of every page confirming FERPA & HIPAA compliance, client-side-only processing, session ID, and UA Research Computing verification
- **Audit Session widget** — fixed bottom-right collapsible panel displaying session ID, start timestamp, data residency status, compliance flags, and SHA-256 readiness
- **File upload with validation** — CSV/JSON uploads are validated for file extension, MIME type, and size (10 MB limit) before any processing begins; a schema reference panel documents the required column format inline
- **Column role assignment** — after a file passes validation, a full-page review screen lets the user confirm or reassign each detected column as Demographic, Outcome, or Identifier before the audit proceeds
- **PII pre-scan** — after column roles are confirmed, every cell is scanned for SSNs (###-##-####), email addresses, phone numbers, and full names; detected values are replaced with `[REDACTED]` and a per-column summary is shown before the audit begins

### Role-Based Access
Users authenticate via a UA NetID login screen before accessing the platform. Three access tiers are enforced:

| Role | Upload access | Export access |
|---|---|---|
| Student | Demo scenarios only; upload form locked | Locked |
| Researcher | Full upload access | Locked |
| Principal Investigator | Full upload access | Full — compliance report export |

### Export
- Plain-text audit report with scenario metadata, metrics, mitigation strategies, ethics analysis, and FAIR scores
- Timestamped with scenario identifier and generation date

---

## Installation & Setup

EquiCare-AI is a single self-contained HTML file. It has no build step, no server, and no package dependencies.

### Requirements
- Any modern browser (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)
- No internet connection required after initial page load (fonts load from Google Fonts CDN on first visit; all other assets are inline)

### Steps

**Option A — Open directly**
```
1. Download equicare-ai-v3.html
2. Open the file in your browser (double-click, or File → Open)
3. The app loads immediately — no server required
```

**Option B — Serve locally (recommended for file upload features)**
```bash
# Python 3
python -m http.server 8080

# Then open: http://localhost:8080/equicare-ai-v3.html
```

**Option C — Clone the repository**
```bash
git clone https://github.com/unserie/equicare-ai
cd equicare-ai
open equicare-ai-v3.html
# or: python -m http.server 8080
```

> **No npm install, no build step, no environment variables.**
> The only external dependency is Chart.js 4.4.1, loaded from cdnjs.cloudflare.com.

---

## Usage Guide

### Running a Demo Audit

1. Open the app in a browser
2. Select your role (Student, Researcher, or Principal Investigator) and click **Sign in with UA NetID**
3. Click **Run audit →** in the navigation bar to reach the Audit section
4. Select one of the five pre-loaded scenario cards:
   - COMPAS Recidivism (Northpointe)
   - Optum Care Management Algorithm
   - Amazon Recruiting Tool (deprecated)
   - Apple Card — Goldman Sachs
   - NIH Funding Allocation Model
5. Click **Run Audit**
6. Watch the 5-stage pipeline progress screen complete
7. Navigate the results using the two-layer tab system:
   - **Audit Metrics** — radar chart, stratified AUC dot plot, SHAP bar chart
   - **Disparate Impact** — diverging dot plots, FPR/FNR slope graph
   - **Mitigation** — effort vs. gain scatter, projected improvement bars, strategy cards
   - **Institutional Impact** — feedback loops, regulatory exposure, recommended actions
   - **FAIR Assessment** — governance-level scoring across Findable, Accessible, Interoperable, Reusable dimensions
8. Click **Export report ↓** to download a plain-text audit report (requires Researcher or PI role)
9. Click **← New audit** to reset

### Running a Custom Audit

> Custom upload requires the **Researcher** or **Principal Investigator** role. Students are restricted to demo scenarios.

1. Sign in and select the Researcher or Principal Investigator role
2. In the **System configuration** form, enter a system identifier (URL, model name, or registry path)
3. Select sector/platform, decision task, and model architecture from the dropdowns
4. Enter the deployed population size
5. Toggle the applicable protected attributes (race/ethnicity, sex/gender, age, SES, etc.)
6. Set deployment stakes (Low / Medium / High / Critical)
7. Select the primary fairness criterion
8. Upload a `.csv` or `.json` dataset using the file upload field; accepted columns are:
   - `outcome` (binary: 0 or 1)
   - `protected_attribute` (categorical, e.g. race or gender)
   - `predicted_score` (numeric, 0–1)
   - `group_label` (string identifier)
9. Confirm column role assignments on the review screen
10. Review the PII pre-scan summary and click **Proceed to audit**
11. Click **Run Audit**

> Custom audits in the current prototype version run the audit pipeline against the closest matching pre-loaded scenario. Live inference against an uploaded model endpoint is a planned feature (see Future Directions). The upload pipeline (column review, PII scanning, redaction) is a live client-side implementation that genuinely reads and processes the uploaded file.

### Reading the Results

| Element | Meaning |
|---|---|
| **Equity Score ring** | 0–100 composite score; red = Critical (<60), amber = Concern (60–79), green = Adequate (≥80) |
| **DIR** | Values below 0.80 indicate adverse disparate impact under the EEOC four-fifths rule |
| **FPR/FNR gap** | Percentage point difference across groups; values above ~5pp are typically actionable |
| **Eq. Odds** | "Not met" means no threshold exists that simultaneously satisfies FPR and FNR parity |
| **SHAP bar color** | Red = high proxy risk (P ≥ 0.50), amber = moderate (0.30–0.49), green = low (<0.30) |
| **FAIR tiles** | Governance scores 0–100; complement, not substitute, for metric-level findings |

Hover over any metric label or data point for a full technical definition and the specific formula being applied.

---

## Technical Implementation

### Architecture

EquiCare-AI is implemented as a single-file HTML application with no external runtime dependencies beyond Chart.js. All state lives in JavaScript — there is no backend, no database, and no API calls during audit execution.

```
equicare-ai-v3.html
├── <style>           CSS custom properties (light + dark theme), layout, components
├── <body>
│   ├── Login screen          Role selector + UA NetID sign-in (first element rendered)
│   ├── Security banner       Compliance status bar (FERPA/HIPAA, session ID, UA verified)
│   ├── Nav, Hero, Stats bar
│   ├── Results screen        Hidden until audit completes; positioned above audit form
│   ├── Audit form            System configuration, file upload, schema panel
│   ├── How it works
│   ├── Conceptual Framework, Footer
│   ├── Column review screen  Full-page column role assignment overlay
│   ├── PII redaction screen  Full-page pre-scan summary overlay
│   └── Audit session widget  Fixed bottom-right collapsible status panel
└── <script>
    ├── SCENARIOS[]            Five fully-specified scenario objects with all metric data
    ├── renderMetrics()        Radar, AUC dot plot, SHAP bar chart (Chart.js)
    ├── renderDisp()           Diverging dot plot and slope graph (inline SVG)
    ├── renderMit()            Mitigation scatter + bars (Chart.js) + strategy cards
    ├── renderImpact()         Institutional impact accordion
    ├── renderFAIR()           FAIR tile grid
    ├── startAudit()           Triggers progress animation → showResults()
    ├── showResults()          Populates all panels from SCENARIOS[curSc]; scrolls to results
    ├── resetAll()             Destroys Chart.js instances, resets DOM, scrolls to audit form
    ├── dlReport()             Constructs and downloads plain-text report blob
    ├── doLogin()              Handles login flow; stores role in appState
    ├── applyRolePermissions() Enforces Student / Researcher / PI access gates
    ├── parseColumns()         Reads CSV/JSON header slice; calls renderColumnReview()
    ├── renderColumnReview()   Builds column role assignment UI
    ├── confirmColumnRoles()   Persists role assignments; calls runPIIRedaction()
    ├── scanAndRedactPII()     SSN/email/phone/name detection and [REDACTED] substitution
    ├── renderPIISummary()     Builds and shows PII findings screen
    ├── proceedAfterRedaction() Stores redacted working copy; proceeds to audit
    └── Utility                initExpandables(), initLitItems(), initTip(), selSc()
```

### Key Design Decisions

**Single-file, zero build-step** — chosen deliberately to maximise reproducibility. Anyone with a browser can run this with no environment setup. The tradeoff is a larger file; this is acceptable for a research prototype.

**Inline SVG for disparity charts** — the diverging dot plot and FPR/FNR slope graph are rendered as inline SVG strings rather than Chart.js instances. This gives pixel-precise control over the chart geometry that Chart.js's declarative API does not expose cleanly for these specific chart types.

**CSS custom properties for theming** — all colors are defined as CSS variables in a single `:root` block with a `@media (prefers-color-scheme: dark)` override, making the theme fully swappable without touching component CSS.

**Scenario data as JS objects** — all five scenarios are fully self-contained objects in the `SCENARIOS` array. Each carries its own metric values, chart data, mitigation strategies, ethics analysis, and FAIR scores. This means the audit "engine" is simply a set of render functions that consume whichever object is selected — no computation occurs at runtime.

**Client-side PII pipeline** — the upload flow (file validation → column role assignment → PII scan → redaction) runs entirely in the browser via FileReader and regex pattern matching. The redacted working copy is stored in memory and discarded on session end; the original file on disk is never modified.

### Dependencies

| Library | Version | Source | Purpose |
|---|---|---|---|
| Chart.js | 4.4.1 | cdnjs.cloudflare.com | AUC dot plot, SHAP bar chart, mitigation scatter and bar charts |
| Fraunces | variable | Google Fonts | Serif display typeface (headings, score) |
| DM Mono | variable | Google Fonts | Monospace body and UI typeface |

No npm packages. No build toolchain. No framework.

---

## Methodology

### Fairness Metrics

**Disparate Impact Ratio (DIR)**
```
DIR = P(Ŷ=1 | A=minority) / P(Ŷ=1 | A=majority)
Threshold: ≥ 0.80 (EEOC four-fifths rule)
```

**Equalized Odds** (Hardt, Price & Srebro, 2016)
```
Requires simultaneously:
  FPR parity: P(Ŷ=1 | Y=0, A=0) = P(Ŷ=1 | Y=0, A=1)
  FNR parity: P(Ŷ=0 | Y=1, A=0) = P(Ŷ=0 | Y=1, A=1)
Note: Mutually incompatible with calibration when base rates differ (Chouldechova, 2017)
```

**Calibration Error**
```
Max |P(Y=1 | Ŷ=s, A=0) - P(Y=1 | Ŷ=s, A=1)| across score deciles s
```

**SHAP Proxy Risk**
```
P(A=sensitive | feature=f) estimated via conditional independence testing
High risk: P ≥ 0.50 | Moderate: 0.30–0.49 | Low: < 0.30
```

### Scenario Data Sources

All metric values in demo scenarios are derived from peer-reviewed published findings, not from original data collection. Each scenario's data object cites its primary sources inline. See the [References](#references) section for full citations.

### FAIR Assessment

The FAIR dimensions are adapted for algorithmic systems following Stoyanovich & Howe (2019) and Gebru et al. (2021):

| Dimension | Algorithmic interpretation |
|---|---|
| **Findable** | Documentation, model cards, audit trail accessibility |
| **Accessible** | Explainability outputs to affected individuals and clinicians |
| **Interoperable** | Portability of fairness findings across deployment contexts |
| **Reusable** | Re-audit cadence, versioning, feedback loop monitoring |

---

## Scenarios & Data Sources

| # | Scenario | Sector | Key Finding | Primary Source |
|---|---|---|---|---|
| 0 | COMPAS Recidivism | Criminal Justice | DIR 0.61; FPR gap 21.4pp Black vs White | Larson et al. (2016); Chouldechova (2017) |
| 1 | Optum Care Management | Healthcare | Healthcare cost proxy encodes race (r=0.68); 27.1pp FPR gap | Obermeyer et al., Science (2019) |
| 2 | Amazon Recruiting Tool | Hiring / HR | DIR 0.55; penalised women's organisations in résumé text | Dastin, Reuters (2018) |
| 3 | Apple Card / Goldman Sachs | Financial Services | Gender-correlated credit limit disparities; NY DFS investigation | Katz (2019); NY DFS (2021) |
| 4 | NIH Funding Allocation | Academic / Public Health | 10.7pp funding gap for Black scientists; topic-choice proxy | Hoppe et al., Science Advances (2019) |

---

## Known Limitations

**Simulated audit engine**. The current prototype renders pre-computed metric values from scenario data objects derived from published literature. It does not perform live statistical computation against an uploaded model or dataset — the audit pipeline animation is illustrative of what a production system would execute. Note that the upload processing pipeline itself (column role assignment, PII detection and redaction) is a live client-side implementation that reads and processes the uploaded file; only the fairness metrics are pre-specified rather than computed. This is the core gap identified in the feedback session: a production-grade audit system requires a benchmark mechanism — a ground-truth reference against which model outputs are evaluated — that this prototype does not yet implement.

**No live model inference**. Custom system configuration populates the form UI but currently maps to the closest pre-loaded scenario for results. Direct API endpoint integration, MLflow registry access, and Hugging Face model card ingestion are planned but not implemented. As raised in the feedback session, auditing proprietary models is additionally constrained by IP restrictions and the absence of model access — a barrier that no client-side tool alone can fully resolve.

**No universal model-agnostic architecture**. The final prototype scopes to five peer-reviewed landmark cases rather than attempting universal coverage.

**Five scenarios only.** The demo library covers five well-documented cases. It does not generalise to arbitrary deployment contexts without extending the `SCENARIOS` array with properly sourced data.

**Intersectional auditing is flagged, not computed.** The platform tags intersectional protected attribute combinations (Race × Gender, Race × SES) and notes their absence in every scenario's audit, but does not compute intersectional subgroup metrics — which would require Kearns et al.'s (2018) two-player game formulation at scale.

**Static SHAP values.** Proxy risk scores (P(sensitive | feature)) are pre-specified per scenario based on published attribution results, not computed from uploaded data via a SHAP explainer.

**Export format is plain text only.** The current export produces a `.txt` file. PDF and structured JSON export with integrity hashing are planned improvements.

---

## Future Directions

1. **Benchmark system** *(highest priority — core gap identified in instructor review)*. The most critical next step is implementing a ground-truth benchmark mechanism: a reference dataset and evaluation standard against which model outputs are compared, analogous to how Arena and LLM stats benchmarks work for capability evaluation. Without this, the audit engine cannot move from literature-derived static values to live computed metrics. This remains the most important unresolved problem in the architecture.

2. **Live metric computation.** Integrate Fairlearn and AIF360 via WebAssembly or a lightweight Python backend (FastAPI) to compute real metrics from uploaded data rather than rendering pre-specified values. This requires the benchmark system above to be in place first.

3. **SHAP computation in-browser.** Run SHAP via a WebAssembly-compiled sklearn pipeline against uploaded CSV/JSON datasets for genuine proxy detection, replacing the current static per-scenario P(sensitive | feature) values.

4. **Intersectional metric computation.** Implement the Kearns et al. (2018) subgroup fairness auditor to compute metrics for intersectional demographic combinations rather than flagging their absence.

5. **Longitudinal re-audit tracking.** Store audit snapshots in IndexedDB to track metric drift over time and flag feedback loop dynamics (D'Amour et al., 2020).

6. **Model card ingestion.** Parse Hugging Face model cards and Mitchell et al. (2019) formatted documentation to auto-populate the system configuration form, reducing the manual input burden for researchers auditing publicly hosted models.

---

## References

Barocas, S., Hardt, M., & Narayanan, A. (2023). *Fairness and machine learning: Limitations and opportunities.* MIT Press. https://fairmlbook.org

Chouldechova, A. (2017). Fair prediction with disparate impact: A study of bias in recidivism prediction instruments. *Big Data, 5*(2), 153–163. https://doi.org/10.1089/big.2016.0047

D'Amour, A., Srinivasan, H., Atwood, J., Baljekar, P., Sculley, D., & Halpern, Y. (2020). Fairness is not static: Deeper understanding of long term fairness via simulation studies. *FAccT 2020.* https://doi.org/10.1145/3351095.3372878

Dastin, J. (2018, October 10). Amazon scraps secret AI recruiting tool that showed bias against women. *Reuters.*

Dressel, J., & Farid, H. (2018). The accuracy, fairness, and limits of predicting recidivism. *Science Advances, 4*(1), eaao5580. https://doi.org/10.1126/sciadv.aao5580

Dwork, C., Hardt, M., Pitassi, T., Reingold, O., & Zemel, R. (2012). Fairness through awareness. *ITCS 2012.* https://doi.org/10.1145/2090236.2090255

Gebru, T., Morgenstern, J., Vecchione, B., Vaughan, J. W., Wallach, H., Daumé III, H., & Crawford, K. (2021). Datasheets for datasets. *Communications of the ACM, 64*(12), 86–92. https://doi.org/10.1145/3458723

Hardt, M., Price, E., & Srebro, N. (2016). Equality of opportunity in supervised learning. *NeurIPS 2016.* https://arxiv.org/abs/1610.02413

Hoppe, T. A., Litovitz, A., Willis, K. A., Meseroll, R. A., Perrin, M. J., Whorton, B. I., & Erosheva, E. A. (2019). Topic choice contributes to the lower rate of NIH awards to African-American/Black scientists. *Science Advances, 5*(10), eaaw7238. https://doi.org/10.1126/sciadv.aaw7238

Kamiran, F., & Calders, T. (2012). Data preprocessing techniques for classification without discrimination. *Knowledge and Information Systems, 33*(1), 1–33. https://doi.org/10.1007/s10115-011-0463-8

Katz, L. (2019, November 10). Apple Card investigated by New York regulator over gender discrimination claims. *Bloomberg.*

Kearns, M., Neel, S., Roth, A., & Wu, Z. S. (2018). Preventing fairness gerrymandering: Auditing and learning for subgroup fairness. *ICML 2018.* https://arxiv.org/abs/1711.05144

Larson, J., Mattu, S., Kirchner, L., & Angwin, J. (2016). How we analyzed the COMPAS recidivism algorithm. *ProPublica.*

Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., & Gebru, T. (2019). Model cards for model reporting. *FAccT 2019.* https://doi.org/10.1145/3287560.3287596

New York State Department of Financial Services. (2021). *DFS investigation of the Goldman Sachs Apple Card credit card algorithm.*

Obermeyer, Z., Powers, B., Vogeli, C., & Mullainathan, S. (2019). Dissecting racial bias in an algorithm used to manage the health of populations. *Science, 366*(6464), 447–453. https://doi.org/10.1126/science.aax2342

Pleiss, G., Raghavan, M., Wu, F., Kleinberg, J., & Weinberger, K. Q. (2017). On fairness and calibration. *NeurIPS 2017.* https://arxiv.org/abs/1709.02012

Stoyanovich, J., & Howe, B. (2019). Nutritional labels for data and models. *IEEE Data Engineering Bulletin, 42*(3).

Wilkinson, M. D., Dumontier, M., Aalbersberg, I. J., Appleton, G., Axton, M., Baak, A., & Mons, B. (2016). The FAIR guiding principles for scientific data management and stewardship. *Scientific Data, 3*, 160018. https://doi.org/10.1038/sdata.2016.18

Zhang, B. H., Lemoine, B., & Mitchell, M. (2018). Mitigating unwanted biases with adversarial learning. *AIES 2018.* https://doi.org/10.1145/3278721.3278779

---

*EquiCare-AI prototype · v3.0 · Demo results derived from published literature · Not for clinical or legal use*
*University of Arizona · College of Public Health*
