Auto Loader is a feature in Databricks designed for efficiently ingesting data from cloud storage, such as Amazon S3 and Azure Blob Storage, into Delta Lake tables. It automates the process of detecting and processing new files as they arrive in the specified storage location, enabling near real-time data ingestion without needing complex management.

### Key Features of Auto Loader

1. **File Notification**:
   - Auto Loader uses file notification services (such as cloud storage event notifications) to automatically identify when new files are added to a directory.

2. **Schema Evolution**:
   - It supports schema evolution, allowing users to handle changes in the incoming data schema seamlessly. This is particularly useful when working with dynamic data sources.

3. **Incremental Processing**:
   - Auto Loader can efficiently process only the new files that have arrived since the last processing job, reducing the amount of data that needs to be read and improving performance.

4. **Data Quality**:
   - Built-in features allow for data validation and quality checks during the ingestion process, helping to ensure that only valid data is loaded into Delta tables.

5. **Integration with Delta Lake**:
   - Auto Loader works natively with Delta Lake, providing benefits such as ACID transactions, time travel, and efficient updates and deletes.

### Example Usage

Here’s a basic example of how to use Auto Loader in a Databricks notebook to ingest data from a cloud storage location into a Delta Lake table:

```python
from pyspark.sql.functions import *
from pyspark.sql.types import *

# Define the schema for the incoming data
schema = StructType([
    StructField("id", IntegerType(), True),
    StructField("name", StringType(), True),
    StructField("timestamp", TimestampType(), True)
])

# Set the source directory for the incoming files
source_directory = "s3://your-bucket/path-to-data/"

# Use Auto Loader to read new files
df = (spark.readStream
      .format("cloudFiles")
      .option("cloudFiles.format", "csv")  # Specify file format
      .schema(schema)                       # Use the defined schema
      .load(source_directory))

# Write the stream to a Delta table
query = (df.writeStream
          .format("delta")
          .outputMode("append")
          .option("checkpointLocation", "s3://your-bucket/checkpoints/")
          .table("your_delta_table"))

query.awaitTermination()
```

### Use Cases

- **Real-Time Data Ingestion**: Ideal for scenarios where new data files are frequently added and need to be processed in near real-time, such as logs, IoT data, or transactional data.
- **Batch Processing with Incremental Loads**: Suitable for batch processing workflows that need to handle incremental loads of data efficiently without reprocessing historical data.

------------------------------------------------------------------

## How autoloader knows whether the data is already processed or not while doing incremental laoding ?

Autoloader in Databricks tracks which files have been processed using **checkpointing**. It records the state of processed files, including their metadata like path, size, and modification time, in a specified checkpoint location. During each run, Autoloader checks this checkpoint to identify and load only new or modified files, ensuring incremental data loading without reprocessing already ingested data. When used with Delta Lake, it also supports deduplication and ensures data consistency through ACID transactions.
Autoloader ensures idempotent writes by integrating with Delta Lake (if used), which supports ACID transactions and deduplication mechanisms. You can use Delta’s merge capabilities or upsert logic to handle duplicates at the record level if necessary.

```
df = (spark.readStream
      .format("cloudFiles")
      .option("cloudFiles.format", "json")
      .option("checkpointLocation", "/path/to/checkpoint")
      .load("/path/to/data"))

df.writeStream
  .format("delta")
  .option("checkpointLocation", "/path/to/delta/checkpoint")
  .start("/delta/target-table")
```

------------------------------------------------------------------

- Note that when using Auto Loader with automatic schema inference and evolution, the 4 arguments shown here should allow ingestion of most datasets. These arguments are explained below.
<img width="650" alt="image" src="https://github.com/user-attachments/assets/ee25d4af-ec53-4beb-aab2-3cb704c3be3d" />


- Example
  ```
  def autoload_to_table(data_source, source_format, table_name, checkpoint_directory):
    query = (spark.readStream
                  .format("cloudFiles")
                  .option("cloudFiles.format", source_format)
                  .option("cloudFiles.schemaLocation", checkpoint_directory)
                  .load(data_source)
                  .writeStream
                  .option("checkpointLocation", checkpoint_directory)
                  .option("mergeSchema", "true")
                  .table(table_name))
    return query
  ```

  ----------------------------------------------------------

