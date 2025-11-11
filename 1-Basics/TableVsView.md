TABLE  VS VIEW


| Aspect | Table | View |
|--------|-------|------|
| Command | CREATE TABLE ... | CREATE VIEW ... AS SELECT ... |
| Purpose | To store and persist data. | To simplify complex queries, restrict data access, and present customized data. |
| Structure | Physical and fixed. | Logical and dynamic (reflects current base table data). |
| Dependencies | None (unless it has foreign keys). | Dependent on the existence and structure of the underlying tables. |