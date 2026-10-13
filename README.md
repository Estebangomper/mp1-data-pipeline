# MP1: End-to-End Data Pipeline

This project is a command-line data pipeline that loads, validates, cleans, and saves tabular data. Data moves through the pipeline in order: the input file and the YAML configuration are checked and loaded, the data is validated, it is cleaned (duplicates, missing values, and outliers), and the cleaned result is saved as a CSV file. `pipeline.py` coordinates these steps and reads the command-line arguments (`--input`, `--config`, `--output`, `--verbose`). The reusable code lives in the `src/` package: `utils.py` sets up logging and checks that input files exist, `data_loaders.py` loads CSV, JSON, and YAML files, `data_validator.py` checks required columns and removes rows with non-numeric values, `data_processor.py` removes duplicates, missing values, and outliers based on the configuration, and `data_output.py` saves the cleaned data. The cleaning settings live in `config/config.yaml`, so the pipeline can process a different dataset by changing the configuration instead of the code. Small sample files for testing are in `fixtures/`, and generated results are written to `output/`, which is not committed to Git.

## Example

```bash
python pipeline.py --input fixtures/sample_data.csv --output output/clean.csv --config config/config.yaml --verbose
```

Output:

```text
DEBUG    __main__ — Arguments parsed: input=fixtures/sample_data.csv, config=config/config.yaml, output=output/clean.csv
INFO     src.utils — Input file validated: fixtures/sample_data.csv
INFO     src.utils — Input file validated: config/config.yaml
INFO     src.data_loaders — Loaded CSV file: fixtures/sample_data.csv (100 rows)
INFO     src.data_loaders — Loaded YAML file: config/config.yaml
WARNING  src.data_validator — Invalid numeric value in rating: not_available
WARNING  src.data_validator — Invalid numeric value in rating: error
DEBUG    src.data_validator — Validation: 100 -> 98 rows
INFO     __main__ — Validation complete: 100 -> 98 rows
DEBUG    src.data_processor — remove_duplicates: 98 → 96 rows
DEBUG    src.data_processor — handle_missing: 96 → 94 rows
DEBUG    src.data_processor — rating: method=iqr, threshold=1.5, removed=2
INFO     __main__ — Processing complete: 98 → 92 rows
DEBUG    src.data_output — Saved 92 rows to output/clean.csv
INFO     __main__ — Saved cleaned data to output/clean.csv
Cleaning report:
{'rows_before': 98, 'rows_after': 92, 'rows_removed': 6, 'columns_before': 5, 'columns_after': 5, 'columns_removed': 0}
```
