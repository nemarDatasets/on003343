[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on003343-blue)](https://doi.org/10.82901/nemar.on003343)

This dataset contains the EEG data used for the study: "Disentangling the percepts of illusory movement and sensory stimulation during tendon vibration in the EEG" (Schneider, C., Marquis, R., Jöhr, J., Da Lopes Silva, M., Ryvlin, P., Serino, A., De Lucia, M., Diserens, K. Unpublished [fill according to following pattern: Journal (Year). https://doi.org/....])

Participants:
Twenty healthy participants (twelve female, eight male), age 24.6 ± 3.2 years, all right-handed. All subjects participated voluntarily and consented in writing to the experiment. The study was covered by the ethical protocol No. 142/09 from the Commission cantonale d'éthique de la recherche sur l'être humain (CER -VD) and in agreement with the Declaration of Helsinki.

Experimental setup: 
The subjects sat comfortably in a chair facing towards their right side so to not see the stimulated left arm, which could have hampered the illusion of movement created during the tendon vibration. While their right arm rested comfortably in the lap, the left arm was supported by a movable forearm rest which allowed two degrees of freedom in the horizontal plane. The reason for this was that proprioceptive feedback of the arm touching an immobile object can prevent the motor illusion from forming.
Subjects wore an EEG cap with built-in wireless amplifier (g.tec Nautilus, g.tec medical engineering, Graz, Austria) with 16 electrodes covering the sensorimotor cortex in the international 10-10 system at positions (Fz, FC3, FC2, FCz, FC2, FC4, C3, C1, Cz, C2, C4, CP3, CP1, CPz, CP2, CP4). The signals were recorded at 500Hz with a hardware-implemented bandpass filter between 0.1 and 100 Hz and sent to a computer in the same room. The reference electrode was placed on the right earlobe.
Tendon vibration was achieved with electromechanical wireless vibrators set into a soft, elastic brace on the left elbow joint (Vibramoov, Techno Concept, Manosque, France). The left arm was chosen since it was demonstrated that illusions start faster and are more vivid in the non-dominant extremity. One vibrator was sitting against the distal biceps tendon and the other against the distal triceps tendon on the same arm. Time information about the beginning of each stimulation was sent via a cable link to the computer and stored with the EEG data.

Study protocol:
EEG was recorded continuously while delivering stimulation sequences consisting of two different vibration types. The first elicited an illusion of elbow extension and was produced by vibrating the distal biceps tendon at 90Hz and the distal triceps tendon at 50Hz. The second produced only a vibration sensation without any movement illusion and consisted of stimulating both tendons at 70Hz. So, the average frequency of stimulation delivered to the agonist-antagonist pair was the same between conditions, but one condition was designed to induce a clear illusion and the other no illusion at all (control).
Each stimulation lasted three seconds and consisted of one second of linear frequency ramp-up, one second of a stable frequency interval and one second of linear frequency ramp-down. The linear ramps started and ended 10 Hz below the target frequency for each stimulation type. The amplitude of the vibration was 2-3 mm. These parameters were based on Romaiguère et al. (2003) and the perception of illusory movement across all subjects was ensured in a pre-screening procedure. This setting was kept constant throughout the whole recording session. 
Each subject underwent three blocks of 72 vibrations (36 illusion, 36 control), arranged randomly and different for each block. The same stimulus sequences were employed for each participant. Inter stimulus intervals varied between one and three seconds and were randomized within and between blocks in order to minimize stimulus onset anticipation.

## NEMAR curation changes (2026-05-21)

BIDS validator: 60 errors + 534 warnings → 0 errors + 473 warnings. Raw `.bdf` binary payloads unchanged.

### `dataset_description.json`
- Added `DatasetType: "raw"`. Why: BIDS-validator otherwise infers a derivative-rules cascade when `DatasetType` is missing alongside `GeneratedBy`, producing spurious warnings.
- Added `GeneratedBy: [{Name: "nemar-cli", Version: "0.8.8", CodeURL: "https://github.com/nemar-org/nemar-cli"}]`. Why: records the NEMAR rehost step in the dataset's provenance chain.
- Bumped `BIDSVersion` `1.4.1` → `1.8.0`. Why: the previous value was below the validator's recognized-version floor.

### `task-fps_channels.json` (new, inheriting root sidecar)
- Created at the dataset root to declare the two non-standard columns used by every per-recording `_channels.tsv`: `software_filters` and `status_description`. Why: every per-recording `channels.tsv` has 11 columns, including these two non-BIDS-standard fields, but none had a paired `_channels.json` sidecar — so the validator could not bind the columns to any schema and fired `TSV_ADDITIONAL_COLUMNS_MUST_DEFINE:software_filters` on every recording (59 errors). One root sidecar inherits to all recordings; the BIDS-canonical columns (`name`/`type`/`units`/`sampling_frequency`/`low_cutoff`/`high_cutoff`/`notch`/`description`/`status`) are intentionally NOT declared here so the validator's built-in schema applies to them.

### `sub-*/ses-*/sub-*_ses-*_scans.tsv` (20 per-recording scan tables)
- `acq_time` cells: appended `.000000` microsecond suffix to every value (e.g. `2019-01-07T13:32:40` → `2019-01-07T13:32:40.000000`), plus a trailing newline. Why: BIDS-EEG downstream tooling (`mne-bids`) requires fractional-second precision on `acq_time` (the standard `strptime` format string is `'%Y-%m-%dT%H:%M:%S.%f'`); without the suffix the loader has to repair the column on every read. Baking the suffix in removes that load-time mutation. No effect on validator findings — this purely cleans the load path. Original timestamp values otherwise unchanged.

### `sub-*/ses-*/eeg/sub-*_ses-*_task-fps_run-*_eeg.json` (59 per-recording sidecars)
- Renamed the key `MiscChannelCount` → `MISCChannelCount`. Why: the previous spelling is not BIDS-canonical (BIDS requires all-uppercase `MISC`); the validator did not recognise the misspelt key and continued to warn that `MISCChannelCount` was missing. No value change — the field stays at `0`. Closes 59 `SIDECAR_KEY_RECOMMENDED:MISCChannelCount` warnings.

### Out of mechanical scope (left as-is)

- 59×2 `TSV_COLUMN_TYPE_REDEFINED:onset` / `:duration` warnings remain. Cause: every per-recording `_events.json` declares `onset` with `Units: "samples (1 sample = 2ms)"` (non-canonical), while BIDS-EEG requires `Units: "s"`. The actual cell values in every `_events.tsv` `onset` column are sample indices (1399, 1801, …), not seconds, so simply rewriting the unit string would lie about the data. The proper fix is a data conversion (divide each `onset`/`duration` cell by 500 Hz and rewrite as seconds) — which is beyond mechanical sidecar curation. Left for a future curation pass.
- 59× recommended-key warnings on `SoftwareVersions`, `Instructions`, `CogAtlasID`, `CogPOID`, `HeadCircumference`, `StimulusPresentation` remain. These are external info (study/lab/equipment details not documented inside this dataset) and inventing values is not defensible.

### Note on EEGDash load-side failures (not addressed here)

Independent of the BIDS-validator findings above, EEGDash currently fails to load all 59 `.bdf` recordings — both MNE-Python and pyedflib reject the BDF headers with byte-count violations. This is a binary-encoding defect in the source `.bdf` files (not a BIDS sidecar issue) and is outside the scope of this curation pass. The validator does not read the BDF binaries, so the dataset's `nemar dataset validate` output here is unaffected.
