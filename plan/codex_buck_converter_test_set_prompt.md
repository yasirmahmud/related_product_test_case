# Codex Task: Generate a Diverse Buck-Converter Cross-Reference Test Set

## Objective

Create a high-quality test set in which:

- **Input:** one Texas Instruments (TI) or Analog Devices (ADI, including legacy Linear Technology / Maxim parts where applicable) **buck converter**
- **Expected output:** one **Monolithic Power Systems (MPS)** buck converter that is the closest defensible functional match
- Every test case must be supported by current, publicly accessible technical sources
- The input products must be highly diverse from one another
- The MPS output must be selected by engineering comparison, not by superficial keyword similarity

This task requires active internet research. Do not rely only on model memory.

---

## Configurable Parameters

Use these defaults unless the user overrides them:

```text
TARGET_CASES = 50
OUTPUT_FORMAT = jsonl
ALLOW_OBSOLETE_INPUTS = false
ALLOW_OBSOLETE_MPS_OUTPUTS = false
MIN_PRIMARY_SOURCE_URLS_PER_CASE = 2
```

If `TARGET_CASES` is changed, preserve all diversity and quality requirements.

---

## Mandatory Web-Research Rules

For every test case:

1. Search the internet for the official TI or ADI product page and datasheet.
2. Search the internet for candidate MPS products.
3. Open and inspect the official MPS product page and datasheet for the selected output.
4. Use official manufacturer sources as the primary evidence.
5. Use distributor or third-party sources only as supplementary evidence.
6. Confirm that all compared ordering families and product numbers actually exist.
7. Confirm product status when possible:
   - Active
   - Not recommended for new designs
   - Last-time buy
   - Obsolete
8. Do not infer specifications from a part-number pattern.
9. Do not invent missing values.
10. Record `null` when a specification cannot be verified from a reliable source.
11. Store direct source URLs for every row.
12. Record the date on which the sources were checked.

A case is invalid if either the input part or the MPS output cannot be verified from an official manufacturer source.

---

## Product Scope

Include only devices whose primary function is a **step-down DC/DC converter**.

Allowed examples:

- Buck regulator IC
- Synchronous buck converter
- Nonsynchronous buck converter
- Buck power module
- Multiphase buck controller or converter
- Dual-output or multi-output buck converter
- Automotive-qualified buck converter
- Digital or PMBus-controlled buck converter
- Ultra-low-IQ buck converter
- High-current point-of-load converter
- High-voltage industrial buck converter

Exclude:

- Pure boost converters
- Pure buck-boost converters
- Flyback converters
- Isolated converters
- LDOs
- LED drivers unless the product is explicitly marketed and specified as a general-purpose buck regulator
- Load switches
- Hot-swap controllers
- Gate drivers without a complete buck-control function
- Parts for which no reasonably related MPS buck solution exists

A device with multiple operating modes may be included only when buck conversion is a primary documented use.

---

## Required Diversity

The test set must not be a collection of near-duplicate parts. Maximize diversity across the complete set.

### Manufacturer balance

Target approximately:

```text
TI inputs: 50%
ADI inputs: 50%
```

A deviation of up to 10 percentage points is acceptable when necessary for quality.

### Diversity dimensions

Distribute cases across as many of these dimensions as possible:

- Input-voltage class
  - Below 6 V
  - 6 V to below 18 V
  - 18 V to below 36 V
  - 36 V to below 60 V
  - 60 V and above
- Output-current class
  - Below 1 A
  - 1 A to below 3 A
  - 3 A to below 10 A
  - 10 A to below 30 A
  - 30 A and above
- Architecture
  - Integrated power MOSFETs
  - External MOSFET controller
  - Power module
  - Multiphase
  - Multi-output
- Control method
  - Constant-on-time
  - Current-mode
  - Voltage-mode
  - Hysteretic or proprietary fast-transient control
  - Digital control / PMBus
- Switching-frequency class
- Quiescent-current class
- Package type and size
- Automotive versus industrial versus consumer versus communications use
- Low-noise versus high-efficiency versus ultra-low-power emphasis
- Fixed-output versus adjustable-output
- Single-output versus dual-output or multi-output
- Feature set
  - Power-good
  - Enable
  - Soft start
  - Synchronization
  - Spread-spectrum operation
  - Forced PWM / pulse-skipping modes
  - Current monitoring
  - Remote sense
  - PMBus or telemetry
  - Functional-safety documentation
- Product age and generation, while respecting lifecycle constraints

### Near-duplicate prevention

Do not include two input parts from the same product family unless they differ materially in at least two major engineering dimensions.

Examples of insufficient differences:

- Same die with only a different package
- Same family with only a small current-rating change
- Fixed-output and adjustable-output variants of the same device
- Automotive and non-automotive variants with otherwise identical behavior

No single TI or ADI family may account for more than 5% of the total test set.

No single MPS part may be used as the expected output for more than 3 test cases unless there is a strong, documented reason. Any reuse must be explicitly justified.

---

## Matching Philosophy

The selected MPS part does not need to be pin-compatible or an exact replacement. It must be the **closest defensible functional counterpart** for the intended application.

Evaluate candidates using the following priorities.

### Hard constraints

A candidate normally fails if it violates any of these:

1. The MPS maximum input voltage is below the input part's documented required operating range.
2. The MPS output-current capability is materially below the input part's rated capability.
3. The MPS output-voltage range cannot support the documented use.
4. The topology or number of outputs is fundamentally incompatible.
5. A mandatory interface, such as PMBus, is absent when it is central to the input part.
6. The MPS device is obsolete when obsolete outputs are disallowed.
7. The MPS product is not a buck converter or buck controller.

A hard-constraint exception is allowed only when no closer MPS product exists and the limitation is clearly documented in `mismatch_notes`.

### Ranked comparison criteria

After hard constraints, compare:

1. Input-voltage range
2. Output-current capability
3. Integrated-FET versus controller versus module architecture
4. Number of outputs and phases
5. Switching frequency and synchronization capability
6. Efficiency and quiescent-current positioning
7. Control architecture and transient-response positioning
8. Package size, thermal capability, and integration
9. Protection features
10. Automotive or industrial qualification
11. Digital interface, telemetry, sequencing, and monitoring
12. Intended application and market positioning

Do not choose an MPS part merely because voltage and current overlap. The rationale must explain why it is the closest option among the candidates reviewed.

---

## Candidate-Selection Process

For each input product:

1. Extract the input product's verified key specifications.
2. Identify at least three plausible MPS candidates when three exist.
3. Compare all candidates against the hard constraints.
4. Rank the surviving candidates.
5. Select the best MPS match.
6. Record the runner-up candidates and concise rejection reasons.
7. Assign a confidence score.
8. Flag cases with meaningful mismatches for human review.

Do not silently accept a weak match.

---

## Required Output

Create:

```text
buck_converter_cross_reference_test_set.jsonl
```

Also create:

```text
buck_converter_cross_reference_summary.md
```

The JSONL file is the machine-readable test set. The Markdown file summarizes coverage, diversity, weak matches, and research limitations.

---

## JSONL Schema

Write exactly one valid JSON object per line.

```json
{
  "case_id": "buck_xref_0001",
  "input_manufacturer": "Texas Instruments",
  "input_part_number": "TPS...",
  "input_product_url": "https://...",
  "input_datasheet_url": "https://...",
  "input_lifecycle_status": "active",
  "input_topology": "synchronous buck converter",
  "input_architecture": "integrated_fet",
  "input_channels": 1,
  "input_phase_count": 1,
  "input_vin_min_v": 4.5,
  "input_vin_max_v": 36.0,
  "input_vout_min_v": 0.8,
  "input_vout_max_v": 30.0,
  "input_output_current_a": 3.0,
  "input_switching_frequency_min_khz": 200,
  "input_switching_frequency_max_khz": 2200,
  "input_iq_typ_ua": null,
  "input_control_method": "current-mode",
  "input_package": "QFN",
  "input_automotive_grade": false,
  "input_key_features": [
    "power-good",
    "spread spectrum"
  ],
  "input_primary_applications": [
    "industrial"
  ],
  "expected_output_manufacturer": "Monolithic Power Systems",
  "expected_output_part_number": "MP...",
  "expected_output_product_url": "https://...",
  "expected_output_datasheet_url": "https://...",
  "expected_output_lifecycle_status": "active",
  "expected_output_topology": "synchronous buck converter",
  "expected_output_architecture": "integrated_fet",
  "expected_output_channels": 1,
  "expected_output_phase_count": 1,
  "expected_output_vin_min_v": 4.5,
  "expected_output_vin_max_v": 36.0,
  "expected_output_vout_min_v": 0.8,
  "expected_output_vout_max_v": 30.0,
  "expected_output_output_current_a": 3.0,
  "expected_output_switching_frequency_min_khz": null,
  "expected_output_switching_frequency_max_khz": null,
  "expected_output_iq_typ_ua": null,
  "expected_output_control_method": null,
  "expected_output_package": "QFN",
  "expected_output_automotive_grade": false,
  "expected_output_key_features": [
    "power-good"
  ],
  "match_type": "closest_functional_match",
  "match_score_0_to_100": 91,
  "confidence": "high",
  "match_rationale": "Concise engineering explanation based on verified specifications.",
  "major_matches": [
    "input-voltage range",
    "output-current capability",
    "integrated synchronous architecture"
  ],
  "mismatch_notes": [
    "MPS device has a lower maximum switching frequency"
  ],
  "alternative_mps_candidates": [
    {
      "part_number": "MP...",
      "product_url": "https://...",
      "rejection_reason": "Lower output-current rating"
    }
  ],
  "diversity_tags": [
    "36V-class",
    "3A-class",
    "industrial",
    "integrated-fet",
    "spread-spectrum"
  ],
  "source_urls": [
    "https://...",
    "https://..."
  ],
  "source_checked_date": "YYYY-MM-DD",
  "needs_human_review": false
}
```

Use normalized units exactly as shown:

- Voltage: V
- Current: A
- Frequency: kHz
- Quiescent current: µA represented numerically in `*_iq_typ_ua`
- Boolean fields: `true` or `false`
- Unknown values: `null`

Do not place units inside numeric values.

---

## Match-Score Rubric

Calculate `match_score_0_to_100` consistently:

```text
20 points: input-voltage compatibility
20 points: output-current compatibility
10 points: output-voltage compatibility
10 points: architecture/topology similarity
10 points: channels/phases similarity
10 points: control and switching behavior
5 points: efficiency/IQ positioning
5 points: package/integration similarity
5 points: qualification/application similarity
5 points: feature similarity
```

Apply substantial deductions for:

- Important missing interfaces
- Lower current capability
- Narrower input range
- Different number of outputs
- External-FET controller matched to an integrated converter, or vice versa
- Automotive input matched to a non-automotive output
- Weak or inaccessible evidence

Interpretation:

```text
90-100: excellent functional counterpart
80-89: strong counterpart with minor differences
70-79: usable counterpart with meaningful differences
60-69: weak counterpart; human review required
Below 60: exclude the case
```

Every included case must score at least 60.

---

## Confidence Rules

Use:

- `high`: official sources verify all critical specifications, and the selected MPS device is clearly the best candidate
- `medium`: critical specifications are verified, but two or more MPS candidates are comparably close
- `low`: evidence is incomplete or the match contains a major architectural difference

Set `needs_human_review` to `true` for every low-confidence case and every score below 75.

Low-confidence cases may account for no more than 10% of the final set.

---

## Validation Requirements

Before finalizing, run programmatic and manual checks.

### Programmatic checks

Verify:

- Valid JSONL syntax
- Unique `case_id`
- Valid and unique input part numbers
- All expected outputs are MPS products
- Required fields are present
- Numeric fields contain only numbers or `null`
- URLs use `http://` or `https://`
- Each case has at least the configured minimum number of primary source URLs
- Match scores are from 60 through 100
- No manufacturer-balance violation beyond the allowed tolerance
- No family exceeds the maximum share
- MPS output reuse limits are respected
- Lifecycle rules are respected
- Diversity-tag coverage is broad
- No exact duplicate input/output pair exists

### Engineering consistency checks

Flag or reject a case when:

- MPS `vin_max` is lower than the input device's `vin_max` without explanation
- MPS current capability is lower without explanation
- The selected MPS product has a fundamentally different topology
- The input has multiple outputs but the MPS part does not
- Automotive qualification is claimed without official evidence
- PMBus, telemetry, or digital control is central to the input but missing in the output
- The rationale makes claims not supported by the recorded sources
- A closer MPS candidate appears to exist but was not evaluated

### URL checks

Attempt to open every URL before completion.

Reject or replace:

- Broken URLs
- Search-result URLs
- Distributor-parametric-search URLs used as primary evidence
- Unofficial PDF mirrors when an official datasheet is available
- URLs that do not resolve to the claimed product

---

## Summary Report Requirements

In `buck_converter_cross_reference_summary.md`, include:

1. Total accepted cases
2. TI versus ADI input count
3. Input-voltage distribution
4. Output-current distribution
5. Architecture distribution
6. Application/qualification distribution
7. Unique input-family count
8. Unique MPS-output count
9. Reused MPS outputs and justifications
10. Match-score distribution
11. Confidence distribution
12. Cases requiring human review
13. Excluded cases and reasons
14. Known research limitations
15. Source-check date range

Include a compact table listing every case:

```text
case_id | input manufacturer | input part | MPS output | score | confidence | human review
```

---

## Research Workflow

Use this sequence:

1. Build a candidate pool substantially larger than `TARGET_CASES`.
2. Gather candidates across the required diversity dimensions.
3. Verify the official input-product sources.
4. Search MPS's site and official documentation for possible counterparts.
5. Compare at least three MPS candidates where possible.
6. Score each proposed match.
7. Remove weak, redundant, unverifiable, or near-duplicate cases.
8. Rebalance the set for manufacturer and engineering diversity.
9. Validate the JSONL programmatically.
10. Write the summary report.
11. Re-open a sample of at least 20% of source links and manually audit the associated rows.
12. Correct all discovered inconsistencies before finishing.

Do not stop after finding the first `TARGET_CASES` pairs. Diversity optimization must happen after assembling a larger pool.

---

## Search Guidance

Useful query patterns include:

```text
site:ti.com buck converter <voltage> <current>
site:analog.com buck regulator <voltage> <current>
site:monolithicpower.com buck converter <voltage> <current>
site:monolithicpower.com <input part number> alternative
site:monolithicpower.com PMBus buck
site:monolithicpower.com automotive buck converter
```

Search by specifications and architecture, not only by competitor part number.

When product-site search is weak, use a general search engine to locate the official product page, then verify the page directly.

---

## Prohibited Behavior

Do not:

- Generate pairs entirely from memory
- Fabricate part numbers, specifications, statuses, or URLs
- Treat parametric overlap as proof of equivalence
- Claim pin-to-pin compatibility without official documentation
- Claim drop-in replacement status unless MPS explicitly documents it
- Use only distributor descriptions
- Select the same general-purpose MPS part repeatedly for convenience
- Fill unknown fields with guessed values
- Hide important mismatches
- Include a case below the minimum score
- Include an input merely to satisfy a diversity quota when the MPS match is poor

---

## Completion Criteria

The task is complete only when:

- The requested number of valid cases has been produced
- Every case has verified official input and MPS sources
- Every selected output is a defensible MPS buck-converter counterpart
- The full set meets the diversity requirements
- The JSONL passes validation
- The summary report is complete
- Weak matches are clearly flagged
- No unsupported technical claims remain

At the end, print a concise completion message containing:

```text
Accepted cases:
Rejected candidates:
TI inputs:
ADI inputs:
Unique MPS outputs:
Cases requiring human review:
JSONL validation:
Source-link audit:
```
