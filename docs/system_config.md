# System Configuration

## Database Settings
Dialect: MSSQL

## Table-Specific Behaviors
# Define tables that require UPSERT (Conflict resolution)
Upsert_Tables:
  - planos
  - prazos
  - planoprodutos
  - produtoscdc
  - clientes

## Operational Rules
# Tables listed here will use the logic defined in rules.md 
# for dynamic Primary Key / Sequence identification.