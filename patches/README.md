# patches

Our changes to the authors' ST-SSDL code (commit `a98331d`), kept as patch files:

1. A flag for the GCRU backbone without deviation learning (no anchor encoder, no prototypes).
2. Controlled seeds (the script no longer overwrites `--seed`).
3. A checkpoint every epoch and a resume flag.
4. Saving the per-window test predictions.
