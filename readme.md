# Data Migration Workflow

🌎 **Choose your language / Escolha seu idioma:**
* [English](#english)
* [Português](#português)

---

## English

This project automates the process of converting CSV data into SQL migration scripts (INSERT/UPSERT) while maintaining schema integrity and dependency order. It is designed to work with AI coding agents to dynamically generate an automation script tailored to your specific database dialect.

### 📋 Prerequisites
To use this workflow, you will need:
* **An AI Coding Assistant / IDE** (like Antigravity, Cursor, Windsurf, GitHub Copilot, or Claude Code) to generate the initial `migrate.py` script.
* **Python 3.x** installed on your machine (to run the local automation script).

### 🚀 Getting Started: Step-by-Step

#### 1. Configure your Database Schema
The file `schema/schema_definitions.sql` acts as the "Brain" of the migration. 
* Paste your standard DDL (`CREATE TABLE`) statements here.
* **Important:** Ensure your `PRIMARY KEY`, `FOREIGN KEY`, and `UNIQUE` constraints (or `UNIQUE INDEX` statements) are included. The script parses these to sort tables by dependency, handle auto-incrementing seeds, and build conflict resolution logic.

#### 2. Prepare your CSV Data
Place your `.csv` files into the `/csv/` folder. 
* **Naming:** The file name must exactly match the table name (e.g., `CLIENTES.csv` for the `CLIENTES` table).
* **Headers:** The first row must contain the column names. These must exactly match the columns defined in your schema.
* **Delimiters:** Ensure your CSV uses a consistent delimiter (the default script expects semicolons `;`).

#### 3. Configure System Behavior
Edit `docs/system_config.md` to define your environment. 

    # Define your target database (e.g., MSSQL, PostgreSQL, MySQL)
    Dialect: MSSQL

    # List tables that require Conflict Resolution (MERGE / IF NOT EXISTS)
    Upsert_Tables:
      - PLANOS
      - PRODUTOSCDC
      - CLIENTES

### 🤖 AI-Agent Workflow (Initial Setup)

Before running the migration, you must generate the `migrate.py` engine. Pass this prompt to your AI coding assistant inside the project directory:

**Prompt:**
> "Read the workflow defined in run.md and the rules in rules.md. Your task is to write a Python script named migrate.py that executes this entire migration process locally. The script must parse schema/schema_definitions.sql for dependencies, primary keys, and unique constraints, read the data from the /csv/ folder, apply the UPSERT logic for tables listed in system_config.md, and output the final SQL files to the /output/ folder using the exact Atomic Write Procedure and data validation checks defined in rules.md. Output ONLY the complete Python code for migrate.py."

### 🐍 Python Automation (Execution)

Once the agent generates `migrate.py`, you can execute the migration locally. This consumes zero AI tokens and scales to massive datasets.

**Usage:**

    python migrate.py

*Note: If you change the `Dialect` in `system_config.md`, you **must** run the AI prompt again to regenerate `migrate.py` with the correct SQL syntax.*

### 📂 Project Structure

* `/csv/` : Input files (must match table names).
* `/docs/` : Configuration and Rules (`rules.md`, `system_config.md`).
* `/output/` : Generated `.sql` scripts and `migration_log.txt`.
* `/schema/` : `schema_definitions.sql` (Single source of truth).
* `migrate.py` : Python automation engine (Generated via AI).

### ⚠️ Critical Warnings

* **Excel Scientific Notation:** The system has a strict guardrail and will abort if it detects scientific notation (e.g., `1,3E+08`). This prevents severe data corruption on large numeric IDs. **Always format your numeric ID columns as "Text" in Excel before saving to CSV.**
* **Regional Settings:** If using Excel in regions that use commas for decimals (like Brazil), save as `.csv` to automatically use semicolons (`;`) as delimiters, or use Power Query to enforce formatting.

---

## Português

Este projeto automatiza a conversão de dados CSV em scripts SQL de migração (INSERT/UPSERT), mantendo a integridade do schema e a ordem de dependência. Ele foi projetado para trabalhar com agentes de IA para gerar dinamicamente um script de automação adaptado ao dialeto do seu banco de dados.

### 📋 Pré-requisitos
Para utilizar este fluxo, você precisará de:
* **Um Assistente de IA / IDE** (como Antigravity, Cursor, Windsurf, GitHub Copilot ou Claude Code) para gerar o script `migrate.py` inicial.
* **Python 3.x** instalado na sua máquina (para executar o script de automação local).

### 🚀 Primeiros Passos: Passo a Passo

#### 1. Configure o Schema do Banco de Dados
O arquivo `schema/schema_definitions.sql` atua como o "Cérebro" da migração.
* Cole suas instruções DDL padrão (`CREATE TABLE`) aqui.
* **Importante:** Certifique-se de que suas constraints de `PRIMARY KEY`, `FOREIGN KEY` e `UNIQUE` (ou instruções `UNIQUE INDEX`) estejam incluídas. O script as analisa para ordenar as tabelas por dependência, lidar com seeds de auto-incremento e criar a lógica de resolução de conflitos.

#### 2. Prepare seus Dados CSV
Coloque seus arquivos `.csv` na pasta `/csv/`.
* **Nomenclatura:** O nome do arquivo deve corresponder exatamente ao nome da tabela (ex: `CLIENTES.csv` para a tabela `CLIENTES`).
* **Cabeçalhos:** A primeira linha deve conter os nomes das colunas. Eles devem corresponder exatamente às colunas definidas no seu schema.
* **Delimitadores:** Certifique-se de que seu CSV use um delimitador consistente (o script padrão espera pontos e vírgulas `;`).

#### 3. Configure o Comportamento do Sistema
Edite `docs/system_config.md` para definir seu ambiente.

    # Defina seu banco de dados alvo (ex: MSSQL, PostgreSQL, MySQL)
    Dialect: MSSQL

    # Liste as tabelas que exigem Resolução de Conflitos (MERGE / IF NOT EXISTS)
    Upsert_Tables:
      - PLANOS
      - PRODUTOSCDC
      - CLIENTES

### 🤖 Fluxo com Agente de IA (Configuração Inicial)

Antes de rodar a migração, você deve gerar o motor `migrate.py`. Passe este prompt para o seu assistente de IA dentro do diretório do projeto:

**Prompt:**
> "Read the workflow defined in run.md and the rules in rules.md. Your task is to write a Python script named migrate.py that executes this entire migration process locally. The script must parse schema/schema_definitions.sql for dependencies, primary keys, and unique constraints, read the data from the /csv/ folder, apply the UPSERT logic for tables listed in system_config.md, and output the final SQL files to the /output/ folder using the exact Atomic Write Procedure and data validation checks defined in rules.md. Output ONLY the complete Python code for migrate.py."

### 🐍 Automação com Python (Execução)

Assim que o agente gerar o `migrate.py`, você pode executar a migração localmente. Este processo não consome tokens de IA e escala para grandes conjuntos de dados.

**Como usar:**

    python migrate.py

*Nota: Se você alterar o `Dialect` em `system_config.md`, você **deve** rodar o prompt de IA novamente para regenerar o `migrate.py` com a sintaxe SQL correta.*

### 📂 Estrutura do Projeto

* `/csv/` : Arquivos de entrada (devem corresponder aos nomes das tabelas).
* `/docs/` : Configurações e Regras (`rules.md`, `system_config.md`).
* `/output/` : Scripts `.sql` gerados e `migration_log.txt`.
* `/schema/` : `schema_definitions.sql` (Fonte única da verdade).
* `migrate.py` : Motor de automação em Python (Gerado via IA).

### ⚠️ Avisos Críticos

* **Notação Científica no Excel:** O sistema possui uma trava de segurança rigorosa e será abortado se detectar notação científica (ex: `1,3E+08`). Isso evita corrupção severa de dados em IDs numéricos grandes. **Sempre formate suas colunas de ID numérico como "Texto" no Excel antes de salvar em CSV.**
* **Configurações Regionais:** Se estiver usando o Excel no Brasil (ou regiões que usam vírgula para decimais), salve como `.csv` para usar automaticamente pontos e vírgulas (`;`) como delimitadores, ou use o Power Query para forçar a formatação.