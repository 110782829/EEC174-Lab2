# EEC174 Lab 2

## Files

- `Lab2.ipynb`: completed lab notebook
- `report.md`: written report
- `data/` and `ece174_pva_*.csv`: required datasets
- `requirements.txt`: Python dependencies

## Run with the course environment

Use the course Docker image `eec174lab1-2:latest`.

Start Jupyter from the repository root, open `Lab2.ipynb`, restart the kernel, and run all cells from top to bottom.

## Run without Docker


```bash
python3.11 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -m notebook
```

Open `Lab2.ipynb` and run all cells.