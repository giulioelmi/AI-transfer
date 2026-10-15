# AI Transfer: Bed Placement Summaries

Groundwork for an AI tool that helps a hospital's transfer center place incoming patients.
It pulls patient data from EHR sandboxes, builds realistic transfer scenarios, and uses an
LLM to turn a full chart into a short, structured bed-placement summary.

All data is from public sandboxes or de-identified research data. Nothing here is a clinical
decision tool.

## What's inside

| File | What it does |
|---|---|
| `explore_cerner.ipynb` | Pulls patients, encounters, conditions, labs, meds and beds from the Cerner (Oracle Health) FHIR R4 sandbox and saves them as CSVs in `data/`. |
| `mimiciv.ipynb` | Queries a MIMIC-IV Postgres database to build `transfer_scenarios.csv` (care units mapped to hospital departments and levels of care) and `patient_charts.json` (one full chart per patient, JSON Lines). |
| `summarize_patient_encounter.py` | Picks one patient and one admission, drops later encounters, and asks the OpenAI API for a schema-checked summary: a short narrative plus yes/no flags for ventilator, isolation, bariatric, LVAD, ECMO and other bed needs, and lists of drips, lines and tubes. |
| `data/RPRB Rule Set.xlsx` | Placement rules used to match patient needs to units. |

Example output: [`data/encounter_summary.json`](data/encounter_summary.json).

## Run it

```bash
pip install openai pydantic python-dotenv pandas requests psycopg2-binary
echo "OPENAI_API_KEY=sk-..." > .env

python summarize_patient_encounter.py data/patient_charts.json \
  --subject-id <subject_id> --hadm-id <hadm_id> --output summary.json
```

`mimiciv.ipynb` needs your own MIMIC-IV database (set `DB_PWORD` in `.env`).

## Team

Giulio Elmi, Max Clark
