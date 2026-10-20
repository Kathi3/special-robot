# Securing the Skies: Anomaly Analysis of ADS-B Surveillance Data

Code for the quantitative part of my MSc dissertation, *Securing the Skies: A Critical Evaluation of ADS-B and ACARS Vulnerabilities and Mitigation Strategies* (MSc Information Security and Digital Forensics, University of East London; submitted January 2025).

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Kathi3/special-robot/blob/main/Masters_Thesis_ADSB.ipynb)

## Problem

ADS-B aircraft broadcasts carry position and kinematic data without encryption or authentication, so received data can be corrupted, replayed or spoofed. This project asks how much of that can be seen in real surveillance data using simple physical-plausibility checks, and where such checks fall short.

## Data

- **Source:** OpenSky Network historical ADS-B state vectors (public, crowdsourced receivers). Only public data is used; no proprietary data is included.
- **Scope:** six files from June 2017 to June 2022, 1,048,575 rows each (6,291,450 records in total).
- **Variables:** time, icao24, lat, lon, velocity, heading, vertrate, callsign, onground, alert, spi, squawk, baroaltitude, geoaltitude, lastposupdate, lastcontact, plus a derived `dataset_year` label.
- **Not redistributed.** See "Getting the data".

## Method

1. Combine the six files and label each record with its dataset year.
2. Profile missing values per variable (0% to 24%).
3. Filter on critical fields (time, icao24, lat, lon, velocity, heading, vertrate, baroaltitude, geoaltitude): 4,744,136 of 6,291,450 records retained (75.41%).
4. Refine: keep airborne records with at least 10 s between consecutive messages per aircraft, leaving 4,716,710 messages.
5. Apply rule-based checks with fixed thresholds (see results).
6. Cross-kinematic section: flag changes in speed, heading, vertical rate, altitude and position between consecutive messages, and count how many flags co-occur.

**Tools:** Python with pandas, NumPy, matplotlib, seaborn and geopy.

## Key results

Share of the 4,716,710 refined messages flagged:

| Check | Threshold | Flagged |
|---|---|---|
| Speed (broadcast velocity) | > 1,200 km/h | 0.018% |
| Vertical rate | abs(vertrate) > 50 m/s | 0.036% |
| Position jump | > 20 km between messages | 0.49% |
| Heading change | > 90° between messages | 0.69% |

Observations:

- Kinematic violations are rare, which indicates internally consistent data. It does not indicate a secure protocol: injected messages can stay within plausible limits and evade fixed-threshold checks.
- **Results depend heavily on the check and the threshold.** An initial speed derived from latitude/longitude differences flagged 9.4% of records, mostly from approximation and time-granularity artefacts; using the broadcast velocity instead flagged 0.018%. Likewise, the fixed 1,000 m position-jump rule in the cross-kinematic section flags 65.6% of messages. That rule is not scaled by the time gap, so it largely reflects normal flight and should not be read as evidence of manipulation.

## Limitations

1. **Fixed thresholds** give no measure of confidence and can miss subtle anomalies or raise false positives.
2. **No ground truth:** anomalies cannot be attributed to attacks, interference or benign transmission errors.
3. **Crowdsourced data:** coverage and quality depend on receiver density and reception conditions.
4. **Known issues:** the first heading check does not handle the 0°/360° wrap-around (the cross-kinematic check does), some `icao24` identifiers appear in scientific notation, and every file has exactly 1,048,575 rows (the spreadsheet row limit). Together, these suggest the files were converted and truncated when opened or saved in a spreadsheet, so each file should be treated as a partial snapshot of its hour.

These limitations motivate uncertainty-aware, time-series-based detection as a next step.

## Getting the data

Register with the OpenSky Network and download the six historical state-vector files named in the notebook (for example, `states_2017-06-26-23.csv`). See the OpenSky documentation for access terms and how to cite them. When running in Colab, upload the files to `/content`.

## Running the analysis

1. Click the Colab badge above (or open `Masters_Thesis_ADSB.ipynb`).
2. Upload the six data files to `/content`.
3. Run all cells (Runtime, then Run all).

## Repository structure

```
Masters_Thesis_ADSB.ipynb   # full analysis notebook
README.md
```

No temporal correlation: results are indicators of forensic relevance, not confirmed incidents.

These limitations motivate uncertainty-aware, time-series-based detection as a next step.
