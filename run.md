# Migration Execution Workflow
1. Load and parse the schema definitions from `schema/schema_definitions.sql`.
2. Determine the dependency-based insertion order by inspecting `FOREIGN KEY` definitions across all tables found in the schema file.
3. For each table found:
    a. Analyze the `CREATE TABLE` and any associated `CREATE INDEX` definitions to discover:
        - PRIMARY KEY column name(s) and auto-increment/identity flags.
        - UNIQUE constraints (inline within the table definition) or external UNIQUE NONCLUSTERED INDEX statements.
    b. Validate CSV Headers: Compare `/csv/[table_name].csv` headers against the table schema. If any column is missing or mismatched, stop and report the error.
    c. If the PK is not auto-incremented, dynamically generate a `SELECT MAX(pk_column)` query as the first step of the generated SQL file to determine the offset.
    d. Load matching CSV data from `/csv/[table_name].csv`.
    e. Map CSV columns to table columns, excluding NULL/empty values.
4. Apply the Atomic Write Procedure defined in `rules.md` to generate the file in the `/output/` folder.
5. (Local Verification): After the atomic rename to `.sql` is complete:
    a. Confirm the row count in the generated SQL file matches the row count of the input CSV.
    b. If the count does not match, log a warning in `output/migration_log.txt` and report the discrepancy.