# DeltaSqlAstBuilder

`DeltaSqlAstBuilder` is a command builder for the Delta SQL statements (described in [DeltaSqlBase.g4]({{ delta.github }}/core/src/main/antlr4/io/delta/sql/parser/DeltaSqlBase.g4) ANTLR grammar).

`DeltaSqlParser` is used by [DeltaSqlParser](DeltaSqlParser.md#builder).

SQL Statement | Logical Command
--------------|----------
 [ALTER TABLE ADD CONSTRAINT](index.md#ALTER-TABLE-ADD-CONSTRAINT) | [AlterTableAddConstraintStatement](../constraints/AlterTableAddConstraintStatement.md)
 [ALTER TABLE DROP CONSTRAINT](index.md#ALTER-TABLE-DROP-CONSTRAINT) | [AlterTableDropConstraintStatement](../constraints/AlterTableDropConstraintStatement.md)
 [CONVERT TO DELTA](index.md#CONVERT-TO-DELTA) | [ConvertToDeltaCommand](../commands/convert/ConvertToDeltaCommand.md)
 [DESCRIBE DETAIL](index.md#DESCRIBE-DETAIL) | [DescribeDeltaDetailCommand](../commands/describe-detail/DescribeDeltaDetailCommand.md)
 [DESCRIBE HISTORY](index.md#DESCRIBE-HISTORY) | [DescribeDeltaHistoryCommand](../commands/describe-history/DescribeDeltaHistoryCommand.md)
 [GENERATE](index.md#GENERATE) | [DeltaGenerateCommand](../commands/generate/DeltaGenerateCommand.md)
 [OPTIMIZE](index.md#OPTIMIZE) | [OptimizeTableCommand](../commands/optimize/OptimizeTableCommand.md)
 [VACUUM](index.md#VACUUM) | [VacuumTableCommand](../commands/vacuum/VacuumTableCommand.md)
