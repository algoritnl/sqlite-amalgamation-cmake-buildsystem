# Changelog

## SQLite Release 3.33.0 On 2020-08-14

1. Support for UPDATE FROM following the PostgreSQL syntax.
2. Increase the maximum size of database files to 281 TB.
3. Extended the PRAGMA integrity_check statement so that it can optionally be limited to verifying just a single table and its indexes, rather than the entire database file.
4. Added the decimal extension for doing arbitrary-precision decimal arithmetic.
5. Enhancements to the ieee754 extension for working with IEEE 754 binary64 numbers.
6. CLI enhancements:
    1. Added four new output modes: "box", "json", "markdown", and "table".
    2. The "column" output mode automatically expands columns to contain the longest utput row and automatically turns ".header" on if it has not been previously set.
    3. The "quote" output mode honors ".separator"
    4. The decimal extension and the ieee754 extension are built-in to the CLI
7. Query planner improvements:
    1. Add the ability to find a full-index-scan query plan for queries using INDEXED BY which previously would fail with "no query solution".
    2. Do a better job of detecting missing, incomplete, and/or dodgy sqlite_stat1 data and generates good query plans in spite of the misinformation.
    3. Improved performance of queries like "SELECT min(x) FROM t WHERE y IN (?,?,?)" assuming an index on t(x,y).
8. In WAL mode, if a writer crashes and leaves the shm file in an inconsistent state, subsequent transactions are now able to recover the shm file even if there are active read transactions. Before this enhancement, shm file recovery that scenario would result in an SQLITE_PROTOCOL error.
