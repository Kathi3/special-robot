# special-robot
Securing the Skies: Anomaly Analysis of ADS-B Surveillance Data

Code for the quantitative part of my MSc dissertation, Securing the Skies: A Critical Evaluation of ADS-B and ACARS Vulnerabilities and Mitigation Strategies (MSc Information Security and Digital Forensics, University of East London; submitted January 2025).

PROBLEM

ADS-B aircraft broadcasts carry position and kinematic data without encryption or authentication, so received data can be corrupted, replayed or spoofed. This project asks how much of that can be seen in real surveillance data using simple physical-plausibility checks, and where such checks fall short.

DATA

Source: OpenSky Network historical ADS-B state vectors (public, crowdsourced receivers). This repository uses only public data; it includes no proprietary data.
Scope: Six data datasets spanning June 2017 to June 2022, with 1,048,575 observations each (6,291,450 records in total).
Variables: time, icao24, latitude, longitude, velocity, heading, vertrate, callsign, onground, alert, spi, squawk, baroaltitude, geoaltitude, lastposupdate, lastcontact, plus a derived dataset_year label.

METHOD

Load and combine the datasets, labelling each record with its dataset year.
Profile missing values per variable (0% to 24% depending on the field).
Filter on critical fields: 4,744,136 of 6,291,450 records retained (75.41%).
Flag rule-based anomalies: speed, heading, vertical rate, and position jumps (changes in reported position inconsistent with the time elapsed between messages). 
Analyse the co-occurrence of anomaly types across the same message sequences.

KEY RESULTS

Speed anomalies: 0.02% of analysed records; vertical-rate anomalies: 0.04%.
Position-jump anomalies were observed at a substantially higher rate than other types. [Add the exact figure from your output.]
Most anomalies occurred in isolation rather than together, suggesting minimal deviations rather than coordinated multi-parameter manipulation.
Low kinematic anomaly rates indicate internally consistent data, but not a secure protocol: injected messages can stay within plausible limits and evade threshold checks.

LIMITATIONS

Crowdsourced data: coverage and quality depend on receiver density and reception conditions.
No ground truth: anomalies cannot be attributed to attacks, interference or benign transmission errors.
Fixed thresholds: predefined kinematic limits can miss subtle anomalies and produce false positives.
No temporal correlation: results are indicators of forensic relevance, not confirmed incidents.

These limitations motivate uncertainty-aware, time-series-based detection as a next step.
