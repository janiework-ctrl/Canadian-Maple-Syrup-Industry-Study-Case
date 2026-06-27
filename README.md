-- ============================================================
-- CANADIAN MAPLE SYRUP INDUSTRY - Case Study Database Schema
-- For use with PostgreSQL / MySQL / SQLite
-- ============================================================

-- --------------------------------------------------------
-- TABLE 1: case_study_sections
-- Stores each phase of the case study as a section
-- --------------------------------------------------------
CREATE TABLE IF NOT EXISTS case_study_sections (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    phase_order INTEGER NOT NULL,          -- Display order (0 = intro, 1 = Ask, etc.)
    phase_label TEXT NOT NULL,             -- e.g. "Phase 1 – Ask"
    title       TEXT NOT NULL,
    body_text   TEXT NOT NULL,             -- Full paragraph content (HTML allowed)
    created_at  DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- --------------------------------------------------------
-- TABLE 2: case_study_links
-- Stores all clickable hyperlinks referenced in the study
-- --------------------------------------------------------
CREATE TABLE IF NOT EXISTS case_study_links (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    section_id  INTEGER NOT NULL REFERENCES case_study_sections(id),
    label       TEXT NOT NULL,             -- Display text for the link
    url         TEXT NOT NULL,             -- Full URL
    description TEXT                       -- Optional tooltip/description
);

-- --------------------------------------------------------
-- TABLE 3: case_study_images
-- Stores image metadata; actual files hosted on GitHub Pages
-- --------------------------------------------------------
CREATE TABLE IF NOT EXISTS case_study_images (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    section_id  INTEGER NOT NULL REFERENCES case_study_sections(id),
    image_order INTEGER DEFAULT 1,
    file_name   TEXT NOT NULL,             -- e.g. "photo1_cleaned_data.png"
    alt_text    TEXT NOT NULL,             -- Accessibility description
    caption     TEXT                       -- Caption shown below image
);

-- ============================================================
-- DATA INSERTS
-- ============================================================

-- --------------------------------------------------------
-- SECTIONS
-- --------------------------------------------------------
INSERT INTO case_study_sections (phase_order, phase_label, title, body_text) VALUES

(0, 'Introduction', 'Canadian Maple Syrup Industry – Case Study',
'In this case study, I act as a junior data analyst within a business intelligence consulting firm. Management has entrusted me with leading a comprehensive analysis project for a new client. I must analyze the Canadian maple syrup industry, studying the relationship between infrastructure deployment (number of taps) and syrup production volume to provide strategic insights on yield predictability. This analysis strictly follows Google''s six-step data analysis process: Ask, Prepare, Process, Analyze, Share, and Act.'),

(1, 'Phase 1 – Ask', 'Business Questions & Stakeholders',
'<p>This case study conducts an initial macroeconomic analysis of the Canadian maple syrup industry by evaluating the historical correlation between the number of taps and total syrup production volume. The goal is to determine whether industry growth relies primarily on a linear increase in exploited resources (more trees) or whether yield is heavily disrupted by external factors.</p>
<ul>
  <li><strong>Objective:</strong> Allow the client to determine whether investing in new maple groves guarantees a predictable increase in production, or whether climate risk entirely dictates the business model.</li>
  <li><strong>Target client:</strong> A group of investors seeking to understand the real growth levers of the maple syrup industry.</li>
  <li><strong>Audience:</strong> The client''s executive team.</li>
</ul>
<h4>Business Questions</h4>
<ol>
  <li>Is there a linear and proportional relationship between the number of taps and maple syrup production in Canada?</li>
  <li>How stable is this relationship over the years (is yield per tap stagnating or progressing)?</li>
  <li>Do major year-to-year production variations stem from tap volume or from external factors (climate, technology)?</li>
</ol>'),

(2, 'Phase 2 – Prepare', 'Data Sources & Credibility',
'<p>The analysis draws on two official public datasets covering a 40-year historical period (1981–2021).</p>
<p>See links section for clickable source references.</p>
<h4>ROCCC Quality Framework</h4>
<ul>
  <li>✅ <strong>Reliable:</strong> Official data from Statistics Canada.</li>
  <li>✅ <strong>Original:</strong> First-hand data collected directly from producers and maple syrup federations.</li>
  <li>✅ <strong>Comprehensive:</strong> Covers all of Canada (Quebec = 70%+ of world production) over four decades.</li>
  <li>✅ <strong>Current:</strong> Includes consolidated census data up to recent cycles.</li>
  <li>✅ <strong>Cited:</strong> Public sources under the Open Government Licence – Canada.</li>
</ul>
<p><strong>Format:</strong> .csv files | <strong>Variables:</strong> Year, Number of taps, Total production (gallons) | <strong>Privacy:</strong> Fully aggregated — no PII.</p>
<h4>Identified Limitations</h4>
<p>Univariate analysis focused on infrastructure (taps). Hidden factors such as seasonal microclimatic variations and vacuum tubing adoption rates are not quantified.</p>'),

(3, 'Phase 3 – Process', 'Data Cleaning & Normalization',
'<p>Although Statistics Canada data is structurally sound, a rigorous normalization phase was necessary:</p>
<ul>
  <li><strong>Data Type Casting:</strong> Removed periods and commas from numeric fields that caused Google Sheets to read values as text.</li>
  <li><strong>Unit Normalization:</strong> Raw production data was in thousands of gallons. Multiplied by 1,000 to obtain true values (e.g., 3,128 → 3,128,000 gallons), yielding a correct rate of 0.1846 gal./tap for 1981 instead of an unreadable 0.00018.</li>
  <li><strong>Temporal Filtering:</strong> Retained only Census of Agriculture years: 1981, 1986, 1991, 1996, 2001, 2006, 2011, 2016, 2021. This enabled a perfect join between both data sources.</li>
</ul>'),

(4, 'Phase 4 – Analyze', 'Analysis & Key Findings',
'<ul>
  <li><strong>Yield per tap:</strong> Created a new column dividing production by taps. Average yield ≈ 0.18 gal./tap, but changes radically year to year.</li>
  <li><strong>Growth rate analysis:</strong> Percentage changes between census years revealed that infrastructure grows steadily while production fluctuates dramatically.</li>
  <li><strong>Anomaly identification:</strong> Historic low in 1986 (0.14 gal./tap) and record high in 2016 (0.26 gal./tap).</li>
</ul>'),

(5, 'Phase 5 – Share', 'Dashboard & Visualizations',
'<p>For the sharing phase, an interactive dashboard was published online using HTML and JavaScript (Chart.js). See links section to access it.</p>
<p>The dashboard includes three visualizations:</p>
<ol>
  <li><strong>Scatter Plot with Regression Line:</strong> Confirms an extremely high correlation (r ≈ 0.97) between taps and production, while showing dispersion that infrastructure alone does not fully explain.</li>
  <li><strong>Yield Volatility Line Chart:</strong> Sawtooth trajectory between 0.14 and 0.26 gal./tap — proving yield risk is dictated by external factors, not infrastructure.</li>
  <li><strong>Combo Chart (Bar + Line, Dual Axis):</strong> Overlays taps and production to immediately highlight anomalies like the 2016 production surge and 1986 historic low.</li>
</ol>
<p><strong>Design choices:</strong> Published online for one-click access on any device. Dark text (brown and red) on light cream background for strong contrast and readability.</p>'),

(6, 'Phase 6 – Act', 'Conclusions & Recommendations',
'<h4>Key Findings</h4>
<ul>
  <li><strong>Infrastructure drives long-term volume growth:</strong> The 0.97 correlation confirms that adding taps increases production over 10–20 year horizons. Purchasing maple groves is a solid long-term strategy.</li>
  <li><strong>Short-term yield is unpredictable:</strong> Over 40% gap between best and worst years means annual revenues cannot be reliably forecasted.</li>
</ul>
<h4>Recommendations</h4>
<ul>
  <li><strong>Build a financial or syrup reserve:</strong> Set aside capital or store syrup barrels during high-yield years (e.g., 2016) to cover expenses during low-yield years (e.g., 1986).</li>
</ul>
<h4>Future Project Ideas</h4>
<ul>
  <li><strong>Weather analysis:</strong> Cross production data with freeze/thaw days in March–April to measure direct temperature impact on sap flow.</li>
  <li><strong>Technology analysis:</strong> Examine whether vacuum tubing and modern concentrators help producers maintain yields despite poor weather.</li>
</ul>');

-- --------------------------------------------------------
-- LINKS
-- --------------------------------------------------------
INSERT INTO case_study_links (section_id, label, url, description) VALUES

(2, 'Statistics Canada – Number of Taps (Table 32-10-0161-01)',
 'https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210016101&pickMembers%5B0%5D=1.1&cubeTimeFrame.startYear=1981&cubeTimeFrame.endYear=2021&referencePeriods=19810101%2C20210101',
 'Census of Agriculture data on maple tap infrastructure, 1981–2021'),

(2, 'Statistics Canada – Maple Syrup Production (Table 32-10-0354-01)',
 'https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210035401&pickMembers%5B0%5D=1.1&cubeTimeFrame.startYear=1981&cubeTimeFrame.endYear=2025&referencePeriods=19810101%2C20250101',
 'Maple syrup production volumes in Canada, 1981–2021'),

(5, 'Live Dashboard – Canadian Maple Syrup Industry',
 'https://janiework-ctrl.github.io/Canadian-Maple-Syrup-Industry/',
 'Interactive dashboard with scatter plot, yield volatility chart, and combo chart');

-- --------------------------------------------------------
-- IMAGES  (upload your photos to /assets/images/ on GitHub)
-- Replace file_name values with your actual filenames
-- --------------------------------------------------------
INSERT INTO case_study_images (section_id, image_order, file_name, alt_text, caption) VALUES

(3, 1, 'photo1_cleaned_dataset.png',
 'Screenshot of the cleaned and normalized dataset in Google Sheets',
 'Fig. 1 – Cleaned dataset after type casting, unit normalization, and temporal filtering'),

(5, 1, 'photo2_scatter_plot.png',
 'Scatter plot with regression line showing correlation between taps and production',
 'Fig. 2 – Scatter Plot (r ≈ 0.97): strong correlation between infrastructure and production'),

(5, 2, 'photo3_yield_volatility.png',
 'Line chart showing yield per tap volatility from 1981 to 2021',
 'Fig. 3 – Yield Volatility Line: sawtooth pattern between 0.14 and 0.26 gal./tap'),

(5, 3, 'photo4_combo_chart.png',
 'Combo bar and line chart on dual axis overlaying taps and production',
 'Fig. 4 – Combo Chart: 2016 production surge and 1986 historic low clearly visible');

-- --------------------------------------------------------
-- SAMPLE QUERY: Fetch all content for website rendering
-- --------------------------------------------------------
-- Get all sections with their links and images:
/*
SELECT
    s.phase_order,
    s.phase_label,
    s.title,
    s.body_text,
    l.label        AS link_label,
    l.url          AS link_url,
    i.image_order,
    i.file_name,
    i.alt_text,
    i.caption
FROM case_study_sections s
LEFT JOIN case_study_links l ON l.section_id = s.id
LEFT JOIN case_study_images i ON i.section_id = s.id
ORDER BY s.phase_order, i.image_order;
*/
