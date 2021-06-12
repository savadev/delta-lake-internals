# Constraints

Starting from 1.0.0 Delta Lake supports the following:

* [Column-Level Invariants](Invariants.md#getFromSchema)
* [Table-Level Check Constraints](Constraints.md#getCheckConstraints)
* [Generated Columns Constraints](../GeneratedColumn.md#addGeneratedColumnsOrReturnConstraints)

Column-level invariants require [Protocol](../Protocol.md) to be at least `2` for the writer version.

## References

* [Constraints](https://docs.databricks.com/delta/delta-constraints.html)