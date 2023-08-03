# Databricks Root Bucket Policies for AWS
Bucket policies are json and it makes no sense why we don't store them in Git!  

Separate directories for different deployments and settings. Each sub-dir has their own README to explain the different arguments and policies. 

### Write Permissions

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0_databricks_dev",`. 

* For Enterprise SaaS Shards, this is a health check file that we test. The Enterprise environments have a health check that shows up in the UI.   
Databricks needs Write / Delete permissions via PutObject and DeleteObject actions for this path. 

`"arn:aws:s3:::DATABRICKS-ROOT/ephemeral/SHARD-NAME/0/*"`. 

This is a link / pointer to /databricks-results. Databricks-results is a folder used by CSV downloads from the display() API in the notebook interface. This is required to allow users to download the results from the webapp. 

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0.*/*"`  

* The 0.* paths contain internal logging information to store metadata and job result data.   
* The job scheduler statistics and run results are stored in the 0.jobs path.   
* The 0.meta path stores points to the mount points.   

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/mnt/"`  

* This path is used for mount points in the platform. https://docs.databricks.com/spark/latest/data-sources/aws/amazon-s3.html#mount-aws-s3   

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/databricks/init/*.sh"`  

* TO DO: Add read only policy for global init scripts. Force admins to write init scripts via S3 tools, but disallow end users of Databricks to write to this path. 
Make this path read-only.

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/databricks/init/*/*.sh"`

* DEPRECATED - Use cluster init-scripts only!
* This path allows users to specify *cluster specific* init scripts only. They cannot deploy global init scripts to the environment. This does allow users to modify the cluster init scripts. Best solution is to use cluster scoped init scripts described below. 
* https://docs.databricks.com/user-guide/clusters/init-scripts.html#init-scripts
* Users can use cluster scoped init scripts available via S3 paths
* https://docs.databricks.com/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/user/hive/warehouse/*.db/"`

* Creating a Database requires a folder creation at the root warehouse directory configured in the environment. 
* Users can only create external tables, e.g. This will make it a conscious decision to write to a table location. 

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/user/hive/warehouse/*.db/*-*"`

* Validation check that delta checks that we have permission to write to. 

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/user/hive/warehouse/*__PLACEHOLDER__/"`

* Validation check when creating Databases in the default hive location. 

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/user/hive/warehouse/*.db/*__PLACEHOLDER__/"`

* Validation check when creating tables within the Database. 

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/tmp/hive/*"`

* Hive tables actions may use the /tmp/ DBFS location for temporary storage. If you use Hive actions, keeping this path available is recommended.  Users may want to read/write to the /tmp/ directory. Organizations may set a TTL for this bucket path if it truly is temporary files. It’s not a required path, and you can require users to write to /tmp in their own S3 buckets.  

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/local_disk0/tmp/*"`

* Used by Structured Streaming to write tmp files to this path. 

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/databricks/mlflow/*",`

* MLFlow artifacts and models are uploaded to this location by the webapp  
The clusters will need read permissions, while the webapp will need read/write/delete

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/pipelines/*",`

* Used by Delta Pipelines to store artifacts for the pipeline

`"arn:aws:s3:::DATABRICKS-ROOT/SHARD-NAME/0/FileStore/*",`

The `/FileStore/` is used for a few different functions within the platform described below.  

  * `/FileStore/plots/` : Stores images created in notebooks when display() is called on a Python/R object from matplotlib or ggplot
  * `/FileStore/jars/` : Library jar jobs, eggs, etc
  * `/FileStore/jars/maven` : Maven jars
  * `/FileStore/job-jars/` : Jar based jobs 
  * `/FileStore/tables/` : Uploads from the Data UI. You can block this path to prevent users from uploading data into the platform via the Data UI import tool. 
  * `/FileStore/import-stage/` : Temporary files created when you import notebooks or DBC files. These temporary files disappear after the notebook import completes.

