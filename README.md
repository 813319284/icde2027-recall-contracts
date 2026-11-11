# Recall Contracts — artifact

Artifact for the ICDE 2027 Research (Round 2) submission

> **Recall Contracts: Anytime-Valid Auditing of Vector Search, and the Price of Compliance**

Everything needed to recompute the paper's numbers is in `ICDE2027-v5.zip`: the LaTeX source and
the PDF, the 9-page supplement, every experiment script, and the JSON/JSONL logs those scripts
produced (including one record per audited query). No number in the paper is typed by hand.

## Quick start (about 3 minutes)

```bash
unzip ICDE2027-v5.zip
cd ICDE2027-v5
bash repro.sh                        # table numbers, prose numbers, template conformance
bash repro/scripts/verify_all.sh     # eleven layers, one summary line per layer
```

Expected last lines:

```
ALL GATES OK
ALL LAYERS OK (submit-ready: 38/38 checks passed)
```

`repro.sh` needs `python3` with `numpy`, and `pdfinfo`/`poppler-utils`. `verify_all.sh` also needs
a LaTeX toolchain (`pdflatex`, `bibtex`) and `qpdf`, because it rebuilds the PDF and checks the
bytes; the gates that need no toolchain are `check_numbers.py`, `check_prose_numbers.py` and
`check_template.py`, which run from the unpacked archive root as
`python3 repro/scripts/check_numbers.py` (and likewise for the other two).

## What is inside

```
paper.pdf                     the submission PDF (13 pages: body ~11.9, references from p. 12)
paper/                        main.tex, refs.bib, 7 vector figures, IEEEtran.cls, SUBMIT.md
supplementary/                supplement.pdf (9 pages: full proofs, protocol, PostgreSQL loop),
                              generated tables, the Docker environment, MANIFEST.txt
repro/scripts/                every experiment and every gate
repro/src/icde_lab/           the library those scripts import
reports/                      every artifact: per-run JSON, per-query JSONL, aggregates
repro.sh                      the three gates that need no LaTeX
SHA256SUMS                    hash of every file in the archive
CHANGELOG-v5.md               what changed relative to the previous version
```

The heavy experiments (index sweeps, the PostgreSQL closed loop) are documented one command per
table or figure in `repro/README.md`; they need the public ann-benchmarks HDF5 datasets and, for
the database loop, Docker.

## Checking one number by hand

`paper/main.tex` states, for the closed loop, the exact certificate's verdicts over three
trajectories. They come from `reports/t3/t3_final_churn_ctrl_rebuild_s{0,1,2}.json`:

```bash
cd ICDE2027-v5
python3 - <<'PY'
import json
tot = {"pass": 0, "reject": 0, "undetermined": 0}
for s in (0, 1, 2):
    d = json.load(open(f"reports/t3/t3_final_churn_ctrl_rebuild_s{s}.json"))
    for r in d["rounds"]:
        tot[r["verdict"]] += 1
print(tot)          # {'pass': 19, 'reject': 0, 'undetermined': 17}
PY
```

## Version

This repository hosts `ICDE2027-v5.zip` (sha256
`c2b972839d5fd31024e55d16af2a98e6879b5385026e968d21fafffbfb71cee3`). The archive's own
`SHA256SUMS` covers every file inside it, and the archive root carries the same hash list.
