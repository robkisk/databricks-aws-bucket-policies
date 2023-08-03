# databricks-aws-bucket-policies
Enterprise 2.0 Databricks Root Bucket Policies 

Arguments:  
* `DATABRICKS_ROOT_S3_BUCKET`: S3 bucket used to deploy Databricks and store artifacts to allow Databricks to operate.  
* `WORKSPACE_ID`: Workspace identification number. 

**Note**:   
* `nvirginia-prod`: Internal naming convention for the Databricks region where this workspace operates. Policy is broken into regions. See the file name to copy the region that matches your deployment.
