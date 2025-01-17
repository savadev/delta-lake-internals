# DeltaTableV2

`DeltaTableV2` is a logical representation of a writable Delta table.

## Creating Instance

`DeltaTableV2` takes the following to be created:

* <span id="spark"> `SparkSession` ([Spark SQL]({{ book.spark_sql }}/SparkSession))
* <span id="path"> Path ([Hadoop HDFS]({{ hadoop.api }}/org/apache/hadoop/fs/Path.html))
* <span id="catalogTable"> Optional Catalog Metadata ([Spark SQL]({{ book.spark_sql }}/CatalogTable))
* <span id="tableIdentifier"> Optional Table ID
* Optional [DeltaTimeTravelSpec](#timeTravelOpt)
* [Options](#options)
* <span id="cdcOptions"> CDC Options (empty in Delta Lake OSS)

`DeltaTableV2` is created when:

* `DeltaTable` utility is used to [forPath](DeltaTable.md#forPath) and [forName](DeltaTable.md#forName)
* `DeltaCatalog` is requested to [load a table](DeltaCatalog.md#loadTable)
* `DeltaDataSource` is requested to [load a table](DeltaDataSource.md#getTable) or [create a table relation](DeltaDataSource.md#RelationProvider-createRelation)

## <span id="options"> Options

`DeltaTableV2` can be given options (as a `Map[String, String]`). Options are empty by default.

The options are defined when `DeltaDataSource` is requested for a [relation](DeltaDataSource.md#RelationProvider-createRelation) with [spark.databricks.delta.loadFileSystemConfigsFromDataFrameOptions](DeltaSQLConf.md#loadFileSystemConfigsFromDataFrameOptions) configuration property enabled.

The options are used for the following:

* Looking up `path` or `paths` options
* [Creating the DeltaLog](#deltaLog)

## <span id="deltaLog"> DeltaLog

`DeltaTableV2` [creates a DeltaLog](DeltaLog.md#forTable) for the [rootPath](#rootPath) and the given [options](#options).

## <span id="Table"> Table

`DeltaTableV2` is a `Table` ([Spark SQL]({{ book.spark_sql }}/connector/Table)).

## <span id="SupportsWrite"> SupportsWrite

`DeltaTableV2` is a `SupportsWrite` ([Spark SQL]({{ book.spark_sql }}/connector/SupportsWrite)).

## <span id="V2TableWithV1Fallback"> V2TableWithV1Fallback

`DeltaTableV2` is a `V2TableWithV1Fallback` ([Spark SQL]({{ book.spark_sql }}/connector/V2TableWithV1Fallback)).

## <span id="timeTravelOpt"> DeltaTimeTravelSpec

`DeltaTableV2` may be given a [DeltaTimeTravelSpec](DeltaTimeTravelSpec.md) when [created](#creating-instance).

`DeltaTimeTravelSpec` is assumed not to be defined by default (`None`).

`DeltaTableV2` is given a `DeltaTimeTravelSpec` when:

* `DeltaDataSource` is requested for a [BaseRelation](DeltaDataSource.md#RelationProvider-createRelation)

`DeltaTimeTravelSpec` is used for [timeTravelSpec](#timeTravelSpec).

## <span id="properties"> Properties

```scala
properties(): Map[String, String]
```

`properties` is part of the `Table` ([Spark SQL]({{ book.spark_sql }}/connector/Table#properties)) abstraction.

`properties` requests the [Snapshot](#snapshot) for the [table properties](Snapshot.md#getProperties) and adds the following:

Name        | Value
------------|----------
 `provider` | `delta`
 `location` | [path](#path)
 `comment`  | [description](Metadata.md#description) (of the [Metadata](Snapshot.md#metadata)) if available
 `Type`     | table type of the [CatalogTable](#catalogTable) if available

## <span id="capabilities"> Table Capabilities

```scala
capabilities(): Set[TableCapability]
```

`capabilities` is part of the `Table` ([Spark SQL]({{ book.spark_sql }}/connector/Table#capabilities)) abstraction.

`capabilities` is the following:

* `ACCEPT_ANY_SCHEMA` ([Spark SQL]({{ book.spark_sql }}/connector/TableCapability#ACCEPT_ANY_SCHEMA))
* `BATCH_READ` ([Spark SQL]({{ book.spark_sql }}/connector/TableCapability#BATCH_READ))
* `V1_BATCH_WRITE` ([Spark SQL]({{ book.spark_sql }}/connector/TableCapability#V1_BATCH_WRITE))
* `OVERWRITE_BY_FILTER` ([Spark SQL]({{ book.spark_sql }}/connector/TableCapability#OVERWRITE_BY_FILTER))
* `TRUNCATE` ([Spark SQL]({{ book.spark_sql }}/connector/TableCapability#TRUNCATE))

## <span id="newWriteBuilder"> Creating WriteBuilder

```scala
newWriteBuilder(
  info: LogicalWriteInfo): WriteBuilder
```

`newWriteBuilder` is part of the `SupportsWrite` ([Spark SQL]({{ book.spark_sql }}/connector/SupportsWrite#newWriteBuilder)) abstraction.

`newWriteBuilder` creates a [WriteIntoDeltaBuilder](WriteIntoDeltaBuilder.md) (for the [DeltaLog](#deltaLog) and the options from the `LogicalWriteInfo`).

## <span id="snapshot"> Snapshot

```scala
snapshot: Snapshot
```

`DeltaTableV2` has a [Snapshot](Snapshot.md). In other words, `DeltaTableV2` represents a Delta table at a specific version.

!!! note "Scala lazy value"
    `snapshot` is a Scala lazy value and is initialized once when first accessed. Once computed it stays unchanged.

`DeltaTableV2` uses the [DeltaLog](#deltaLog) to [load it at a given version](#getSnapshotAt) (based on the optional [timeTravelSpec](#timeTravelSpec)) or [update to the latest version](#update).

`snapshot` is used when `DeltaTableV2` is requested for the [schema](#schema), [partitioning](#partitioning) and [properties](#properties).

## <span id="timeTravelSpec"> DeltaTimeTravelSpec

```scala
timeTravelSpec: Option[DeltaTimeTravelSpec]
```

`DeltaTableV2` may have a [DeltaTimeTravelSpec](DeltaTimeTravelSpec.md) specified that is either [given](#timeTravelOpt) or [extracted from the path](DeltaTableUtils.md#extractIfPathContainsTimeTravel) (for [timeTravelByPath](#timeTravelByPath)).

`timeTravelSpec` throws an `AnalysisException` when [timeTravelOpt](#timeTravelOpt) and [timeTravelByPath](#timeTravelByPath) are both defined:

```text
Cannot specify time travel in multiple formats.
```

`timeTravelSpec` is used when `DeltaTableV2` is requested for a [Snapshot](#snapshot) and [BaseRelation](#toBaseRelation).

## <span id="timeTravelByPath"> DeltaTimeTravelSpec by Path

```scala
timeTravelByPath: Option[DeltaTimeTravelSpec]
```

!!! note "Scala lazy value"
    `timeTravelByPath` is a Scala lazy value and is initialized once when first accessed. Once computed it stays unchanged.

`timeTravelByPath` is undefined when [CatalogTable](#catalogTable) is defined.

With no [CatalogTable](#catalogTable) defined, `DeltaTableV2` [parses](DeltaDataSource.md#parsePathIdentifier) the given [Path](#path) for the `timeTravelByPath` (that [resolvePath](DeltaTimeTravelSpec.md#resolvePath) under the covers).

## <span id="toBaseRelation"> Converting to Insertable HadoopFsRelation

```scala
toBaseRelation: BaseRelation
```

`toBaseRelation` [verifyAndCreatePartitionFilters](DeltaDataSource.md#verifyAndCreatePartitionFilters) for the [Path](#path), the [current Snapshot](SnapshotManagement.md#snapshot) and [partitionFilters](#partitionFilters).

In the end, `toBaseRelation` requests the [DeltaLog](#deltaLog) for an [insertable HadoopFsRelation](DeltaLog.md#createRelation).

`toBaseRelation` is used when:

* `DeltaDataSource` is requested to [createRelation](DeltaDataSource.md#RelationProvider-createRelation)
* `DeltaRelation` utility is used to `fromV2Relation`
