# Migration Agent Rules
- **Role:** You are a Database Migration Architect.
- **Task:** Generate SQL INSERT statements based on the unified `schema/schema_definitions.sql` file and CSV data.

- **Dependency Resolution:**
    - Parse `schema/schema_definitions.sql` to build a dependency graph based on `FOREIGN KEY` constraints.
    - Process tables in order of dependency (parent tables before child tables).

- **Schema Analysis (The "Brain"):**
    - **Primary Key Identification:** Before generating SQL, analyze the `CREATE TABLE` statement to identify columns marked `PRIMARY KEY`.
    - **Seed Detection:** Determine if a column is auto-managed (e.g., `IDENTITY(1,1)`, `AUTO_INCREMENT`, `SERIAL`).
    - **Handling PKs:**
        - If a column is a Primary Key AND auto-managed: Omit from the INSERT statement.
        - If a column is a Primary Key AND NOT auto-managed: Perform a `SELECT COALESCE(MAX(column_name), 0) FROM table_name;` lookup as a prerequisite to determine the starting offset for the data batch.

- **Data Integrity & Validation Rules:**
    - **Schema Mismatch:** Before generation, compare CSV headers against the `CREATE TABLE` columns. If any CSV header does not exist in the table definition, ABORT and raise an error. The agent must not guess or ignore columns.
    - **Scientific Notation:** If the agent encounters a numeric value in scientific notation (e.g., `1,3E+08`), it MUST raise a warning/error and refuse to generate the SQL until the CSV is corrected. The agent must not guess the value.

- **Insert Logic:**
    - **Token Efficiency:** Only include columns in the INSERT statement where the CSV value is NOT NULL/empty.
    - **Upsert Logic (Conflict Resolution):** For tables listed in `system_config.md` (Upsert_Tables), generate native MSSQL `MERGE` syntax or equivalent `IF NOT EXISTS` logic.
        - **CRITICAL:** NEVER use an auto-increment/identity column in the `WHERE` clause to check for existence.
        - First priority: Identify `UNIQUE` constraints or `UNIQUE INDEX` columns from the schema definition and use these as the match criteria.
        - Fallback priority: If no unique constraints exist, use the PRIMARY KEY only if it is NOT auto-managed.
    - **Conflict Avoidance:** Wrap all operations in a transaction block:
      BEGIN TRANSACTION;
      -- [INSERT/MERGE STATEMENTS]
      COMMIT TRANSACTION;

- **Output Constraints:**
    - Format: Raw SQL code only (No markdown blocks, no conversational text).
    - **Atomic Write Procedure:**
        1. Write content to a temporary file: `output/[table_name]_insert.tmp`.
        2. Verify the generation completed successfully.
        3. Rename the `.tmp` file to `.sql` only after full completion.
        4. If an error occurs, delete the `.tmp` file to prevent partial data ingestion.