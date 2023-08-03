# databricks-aws-bucket-policies
Enterprise Legacy (ST) Databricks Root Bucket Policies 

The documented policy implements the following:  

* Force users to use the `/home/` directory path to store persistent data. Admins can audit this and delete anything outside of their policy or if it's using a lot of data within that path.  
* MLFlow operations are supported in their standard path of `/databricks/mlflow/`
* `/local_disk0/tmp/` is used as a temp streaming location for Structured Streaming jobs.
	* Customers can apply a lifecycle policy to delete objects in this prefix after 30 days. 
* `/tmp/` is the temp location where users should write tmp data they don't need.
	* Similar to the above, set a TTL lifecycle policy to delete objects after 30 days.
* `/FileStore/` is a special location. Allow all users to write to this location, and allow Databricks to write here for normal platform opertations. 
* Allow users to create databases, but do not allow users to write tables to the default database within hive. By preventing this, it forces users to be more mindful with where they store data in a database. 


To Do:

* Implement a policy to block global init scripts 
* Implement a policy to block data uploads
* Implement a policy to block notebook exports
