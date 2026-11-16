# Player Cemetery

Recorded Angband play: human sessions and bounded headless games. The code is
in diegoami/angband-recorder and diegoami/borg-rs.

The recording launchers use this checkout as BORG_PLAY_DATA_DIR and its deaths/
subdirectory as BORG_DEATH_RECORDS_DIR.

- recordings/*.zip and recordings/*.json: committed recording exports and build identities.
- deaths/: links to canonical cases in diegoami/borg-cemetery.
- save/, user/, archive/, panic/, scores/ and recordings/SESSION/: local play state and full recording bundles, ignored by Git.

From borg-rs, start-recording.sh NAME records human play with an optional Borg.
record-random-runs.py --runs N records fresh random headless games. Add
--auto-push to publish each completed session archive to this checkout's origin.
The publisher commits explicit exports and the associated death dossier only.
Executables and live profiles remain local; restore an archive with its matching
executable before verified replay. Recordings retain their engine/recorder
licences and provenance. Replays and validation repeats are not independent lives.

Incident analysis, reusable scenarios and trial reports belong in
[Borg Cemetery](https://github.com/diegoami/borg-cemetery). Original published
recording archives and working player state remain here.
