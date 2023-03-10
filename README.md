# DP-203-Notes

## 1) Distributing Data in Synapse Analytics

### Round-Robin Distributed

Data is distributed evenly in a random fashion

Best for: 
1) There is no clear distribution key
2) You don't have frequent joins with other tables
3) Uniform distribution 
4) Temporary staging table
5) A simple starting point

<br>

### Hash Distributed

Data is distributed deterministically by using a hash function

Best for: 
1) Large tables like fact tables or historical transaction tables
2) Tables with frequent inserts, updates and deletes

Example of DDL
```

CREATE TABLE YOUR_TABLE 
(
    COLUMN1 INT NOT NULL, 
    COLUMN2 INT NOT NULL, 
    COLUMN3 INT NOT NULL, 
    COLUMN4 INT NOT NULL, 
    COLUMN5 VARCHAR(20) 
)
WITH 
(
    CLUSTERED COLUMNSTORE INDEX
    DISTRIBUTION = HASH(COLUMN1)
)

```
<br>

### Replicated

Full copy of the table is replicated to every node

Best for: 
1) Small lookup of dimension tables that are frequently joined with larger tables

<br>

## 2) Pruning Data


### Data Skipping

Data skipping is automatically collected when data is written into a Delta Table

Delta Lake on Databricks uses the minimum and maximum values to speed up queries

<br>

### Z-Ordering

Is a technique for colocating related information in the same set of files and it is automatically used by the data-skipping algorithms of delta lake on databricks to substantially reduce the amount of data to be read

<br>

### Dynamic File Pruning (DFP)

Allows files to be skipped within partitions

It is good for non partitioned tables, of for joins of non-partitioned columns

```
spark.databricks.optimizer.dynamicParitionPruning
```

```
spark.databricks.optimizer.deltaTableSizeThreshold
```

```
spark.databricks.optimizer.deltaTableFilesThreshold
```
<br>

## 3) Compressing Data

<br>

### Compression Options

<br>

**Rowstore**
- Row Compression
- Page Compression

**Columnstore**
- Columnstore compression by default
- Columnstore archival compression


<br>

### Compression of Rowstore objects

- Available in azure SQL database
- Row or page compression can be enabled or disabled both online or offline
- Disk space requirements when enabling or disabling are the same as when you are creating or rebuilding an index

<br>

Example

```
ALTER TABLE TABLE1 REBUILD PARTITION = ALL WITH (DATA_COMPRESSION = ROW)
```

<br>

### Compression of Columnstore objects


- Is enabled by default
- Used by clustered index or nonclustered columnstore index
- Indexes with columnstore archival compression are slower thant those without it
- Use only to reduce the storage size of data that is not accessed frequently
