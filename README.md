# Spark
Spark on Dataproc

Contact me to request access to my code:  
[indianaajoness@yahoo.com](mailto:indianaajoness@yahoo.com)

# Dataproc

This repository contains the necessary artifacts to spin up an ephemeral Dataproc cluster and run a PySpark job.

## Folders and Files

### `apps/pyspark`

#### `pyspark_sql.py`

This file contains an example of using Spark SQL within a PySpark job.

- Directly references a fully qualified table name.

#### `pyspark_sql_tempview.py`

This file demonstrates the use of Spark SQL within a PySpark job utilizing a Spark TempView.

- Creates a `TempView` that is referenced in subsequent SQL commands.

#### `pyspark_dataframe_api.py`

This script demonstrates the use of the Spark DataFrame API:

- Utilizes chained methods like `.filter()`, `.groupBy()`, `.orderBy()`, etc., instead of using SQL syntax.

#### `pyspark_sql_JOIN_2_tables.py`

This script demonstrates a JOIN operation between two tables.

**Performance Note:**  
Both Spark SQL and the DataFrame API deliver similar performance within Spark. However, creating a `TempView` incurs a small memory overhead.

### `gcs_key_encode`

#### `json_base64_encode.py`

This file converts your GCS service account JSON key into a base64 encoded gzip file. Once you create your service account and key, use this script to convert the JSON into a gzip base64 encoded string.  
Upload the encoded string to **Google Secret Manager**.  
The PySpark apps include details on how the **get_secret** method retrieves secrets from Google Secret Manager.

### `gsutil_commands`

#### `copy_up_to_gcs_bucket.sh`

This file contains `gsutil` commands that can be used to copy your PySpark app and initialization script to GCS. It is necessary to have these files in GCS.

### `initialization`

#### `init.sh`

The **init.sh** script is run locally in an Ubuntu 22 Docker container. Ubuntu 22 is one of the base images used in these checkout tests. It is often necessary to create a JAR file for your Spark app. This script can be used to build that JAR, which can then be copied to GCS for use during Spark submit.  
This **init.sh** script was used for local checkout of some of the commands needed in the **initialization_script.sh**.

#### `initialization_script.sh`

The **initialization_script.sh** script is executed at cluster creation. It is the minimum required for configuring nodes in our Dataproc cluster to work with our Spark app.

The initialization script is required at cluster creation and will run on every node in the cluster, including the master and all worker nodes.  
The script performs the following tasks:
- Installs a specific version of Java (version 11).
- Creates environment variables.
- Installs additional libraries.

Note: As of PySpark 3.5, the default Python version supported and used is Python 3. However, if you have specific requirements for a particular minor version of Python 3 (such as Python 3.8 or 3.9), you may need to configure this explicitly in your Dataproc cluster setup or in your job submissions.

### `startup`

#### `debian_12_hadoop3_3_spark_3_5.sh`

This `gcloud` startup command creates a Dataproc cluster with the base image Debian 12, Hadoop 3.3, and Spark 3.5.

#### `ubuntu_22_hadoop3_3_spark_3_3.sh`

This `gcloud` startup command creates a Dataproc cluster with the base image Ubuntu 22.04, Hadoop 3.3, and Spark 3.3.

Note that the same **initialization_script.sh** is used for both base images.

### `submit`

#### `gcloud_submit.sh`

The `gcloud` command to submit a PySpark job to a Dataproc cluster:

```sh
gcloud dataproc jobs submit pyspark --cluster=cluster-abcdefg --region=us-central1 --jars=gs://bucket/path/file.jar --files=gs://bucket/path/pyspark_app.py
```


This command launches your PySpark app on the Dataproc cluster.  
Note that **--cluster=** specifies the name of your Dataproc cluster.  
The command also specifies the JAR file to use. I found that providing the JAR file in the submit command worked when specifying it in the SparkSessionBuilder config did not.  
**--files** points to your PySpark app copied to GCS.

