# Change Data Capture / Replication on going:


Detect the change automatically and reflect it in the output file without any human intervention.

Change data capture = In simple words, if we load all the data from rdbms like mysql to s3. if we add one row in mysql database it should automatically reflect on s3 is called change data capture.


## Steps Involved:
1. Create a data lake.
1. If there is any change in the data i.e. insertion, updation and deletion 
1. Take all those changes from the database, develop a pipeline which will consider  all the changes.

## Step by step process:
#### 1. Create an RDS instant.
    * Select a database e.g MySql db, select freetier facility, disable auto scaling and automonitoring etc. 
    * DB parameter group could be default MySql group
    * Enable the backup option
    * Create a new paramter group and use it here.
   
#### 1. Create a S3 bucket.
     * It should have a unique name.
     * You can disable the public access.
     * Acknowledge and create.


#### 1. Creating source and destination endpoint as well as replication instance 
      * Creating a source endpoint:
         *  DMS - endpoint - source endpoint
         * select RDS DB instance - name of the instance
         * provide access info manually
         * Test it with the replication instance
         * Change database-1 name to anything you like
         * At last create.

      * Creating a destination endpoint:
         * Similar procedure as source endpoint.
         * Only change is to create an IAM role -> Give s3 full aceess to that IAM role and provide arn in the endpoint.

      * Creating a Replication instance:
         * Select t2.micro (comes under free tier), 50 GB, single AZ and create.

