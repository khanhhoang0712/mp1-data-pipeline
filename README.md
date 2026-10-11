# MP1 Data Pipeline

This project is a command-line data pipeline that loads a data file, validates it, cleans it, and saves the cleaned result as a CSV. The user runs `pipeline.py` with an input file, an output path, and a YAML configuration file that controls how the data is checked and cleaned, so the same code can clean different datasets without being changed. Data moves through the pipeline in order: the input and configuration files are checked and loaded, the data is validated, then cleaned, then saved, and a cleaning report is printed at the end. `pipeline.py` coordinates these steps, while the reusable modules live in the `src/` package. `utils.py` sets up logging and checks that input files exist, and `data_loaders.py` loads CSV, JSON, and YAML files based on their file extension. `data_validator.py` checks that required columns exist and removes rows with non-numeric values in numeric columns, and `data_processor.py` removes duplicate rows, rows with missing values, and outliers based on the settings in `config/config.yaml`. Finally, `data_output.py` saves the cleaned data, creating the output folder if it does not exist.

## Example

```
python pipeline.py --input fixtures/sample_data.csv --output output/clean.csv --config config/config.yaml --verbose
```

Output:

```
15:58:56 DEBUG    __main__ — Arguments parsed: input=fixtures/sample_data.csv, config=config/config.yaml, output=output/clean.csv
15:58:56 INFO     src.utils — Input file validated: fixtures/sample_data.csv
15:58:56 INFO     src.utils — Input file validated: config/config.yaml
15:58:56 INFO     src.data_loaders — Loaded CSV file: fixtures\sample_data.csv (100 rows)
15:58:56 INFO     src.data_loaders — Loaded YAML file: config\config.yaml
15:58:56 WARNING  src.data_validator — Invalid numeric value in rating at row 94: not_available
15:58:56 WARNING  src.data_validator — Invalid numeric value in rating at row 95: error
15:58:56 WARNING  src.data_validator — Removed 2 rows with invalid numeric values in rating
15:58:56 DEBUG    src.data_validator — Validation: 100 -> 98 rows (removed 2)
15:58:56 INFO     __main__ — Validation complete: 100 -> 98 rows
15:58:56 DEBUG    src.data_processor — remove_duplicates: 98 -> 96 rows (removed 2)
15:58:56 DEBUG    src.data_processor — handle_missing: 96 -> 94 rows (removed 2)
15:58:56 DEBUG    src.data_processor — rating: method=iqr, threshold=1.5, removed=2
15:58:56 INFO     __main__ — Processing complete: 98 -> 92 rows
15:58:56 DEBUG    src.data_output — Saved 92 rows to output\clean.csv
15:58:56 INFO     __main__ — Saved cleaned data to output\clean.csv
Cleaning report:
{'rows_before': 98, 'rows_after': 92, 'rows_removed': 6, 'columns_before': 5, 'columns_after': 5, 'columns_removed': 0}
```