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

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 60 errors + 534 warnings to 0 errors + 474 warnings. None of the raw `.bdf` files were modified — every change is to a text sidecar.

**Dataset description (`dataset_description.json`)**
- Added `DatasetType: "raw"` so the dataset is validated as raw data rather than a derivative.
- Updated `BIDSVersion` from `1.4.1` to `1.11.1` (the version the current validator checks against).
- `GeneratedBy` was left absent, exactly as the source published it — nothing was added there.

**Channel-column documentation (`task-fps_channels.json`, new, at the dataset root)**
- Every recording's channel table has two columns BIDS does not define — `software_filters` and `status_description` — but no sidecar described them, so the validator rejected those columns on every recording. A single root-level sidecar now describes both columns and applies to all recordings at once. The standard channel-table columns are deliberately left undeclared so the validator's built-in rules apply to them.

**Recording sidecars (`_eeg.json`, all 59 recordings)**
- The misc-channel-count field was spelled `MiscChannelCount`; BIDS uses the all-uppercase `MISCChannelCount`. It was renamed (the value, `0`, was already correct) so the validator recognizes it.

**Acquisition times (`scans.tsv`) — left exactly as published**
- EEGDash's loader appends a `.000000` microsecond suffix to the acquisition times when it reads the files, but the published timestamps (e.g. `2019-01-07T13:32:40`) are already valid BIDS — fractional seconds are optional — so they were left unchanged rather than having the loader's suffix baked in.

**Out of mechanical scope (left as-is)**
- Every event table stores its `onset` and `duration` as sample numbers (e.g. `1399`, `1801`), and the matching sidecars label the unit accordingly ("samples", where 1 sample = 2 ms) rather than in seconds as BIDS expects. Simply relabeling the unit as seconds would misstate the data; converting it correctly means dividing every value by the 500 Hz sampling rate, which is a data change beyond this text-only cleanup. The mismatch was left in place and flagged here.
- A set of "recommended but missing" equipment and study fields (software versions, instructions, head circumference, cognitive-atlas IDs, stimulus-presentation details) were left blank rather than filled with guesses. This includes `GeneratedBy`: the source does not document what tool generated the data, so it was left absent rather than invented.

**The recording files cannot be loaded (not a BIDS issue)**
- Separately from the validator findings above, all 59 `.bdf` files are rejected by both MNE-Python and pyedflib because their headers violate the BDF byte-count rules. This is a defect in the source recording files, not in the BIDS sidecars, and is outside this metadata cleanup. The BIDS validator does not read the binaries, so its result is unaffected; the files would need re-encoding before the signal can be opened.
