# Change Data Capture / Replication on going:


Detect the change automatically and reflect it in the output file without any human intervention.

Change data capture = In simple words, if we load all the data from rdbms like mysql to s3. if we add one row in mysql database it should automatically reflect on s3 is called change data capture.


## Steps Involved:
1. Create a data lake.
1. If there is any change in the data i.e. insertion, updation and deletion 
1. Take all those changes from the database, develop a pipeline which will consider  all the changes.

## Step by step process:
#### 1. Create an RDS instant. [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/01.Create%20an%20RDS%20instant.)
    * Select a database e.g MySql db, select freetier facility, disable auto scaling and automonitoring etc. 
    * DB parameter group could be default MySql group
    * Enable the backup option
    * Create a new paramter group and use it here.
   
#### 2. Create a S3 bucket. [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/02.Create%20a%20S3%20bucket.)
     * It should have a unique name.
     * You can disable the public access.
     * Acknowledge and create.


#### 3. Creating source and destination endpoint as well as replication instance [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/03.Creating%20source%20and%20destination%20endpoint%20as%20well%20as%20replication%20instance)
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

#### 4. Connect AWS with MySql DB: [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/04.Connect%20AWS%20with%20MySql%20DB)
      * Download mysql workbench.
      * Open -> Create a new connection 
      * Provide a name, hostname: endpoint, port: 3306, provide username and password
      * Test the connection.
      * _In case your connection failed, go to EC2 dashboard, click on security group, then inbound rule -> add -> custom TCP, port 3306, 0.0.0.0/0 and click on save._

#### 5. Schema design [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/05.Schema%20design)
      * Create a schema and data in a file.
      * Don't forget to add a primary key otherwise it might give us error while changing the data

#### 6. Create Data Migration task: [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/06.Create%20Data%20Migration%20task)
      * Create data migration task under DMS.
      * Provide name, replication instance, source and destination endpoint
      * Migration type will be *"Migrate existing and replicate ongoing changes."* This is important otherwise it will not replicate the new changes.
      * Add new section rulw: Provide schema name, table name, use "*" so that it will take all schemas or tables.
      * After creating this successfully, it will automatically load all the data from mysql to s3.

#### 7. DMS replication ongoing: [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/07.DMS%20replication%20ongoing)
      * Try using some updation, deletion, insertion operation in mysql and see it automatically reflecting in your s3 bucket. It will also create a new file to show what we have changed.
      * *Before continuing stop the rds and dms instance.*
      
#### 8. Create a lambda function: [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/08.Create%20a%20lambda%20function) [code](https://github.com/kaustubh16-dev/Change-Data-Capture/blob/main/Images_and_Code/08.Create%20a%20lambda%20function/lambda_function.py)
      * Create IAM role: S3fullaccess, gluefullaccess, cloudwatch full access.
      * Add trigger -> s3 -> bucketname
      * Here we cannot directly add destination, we have to create a seperate glue job then invoke it.
      * Check lambda function by deploying any code ("Hello World"). and check on cloudwatch -> log groups.
      
#### 9. Creating a glue job [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/09.Creating%20a%20glue%20job)
      * Create an IAM role: s3fullaccess, cludwatchfullaccess
      * Go to glue -> add job -> spark 2.4
      
#### 10. Adding invoke for glue job. [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/10.Adding%20invoke%20for%20glue%20job)
      * We can't directly add destination of glue job.
      * Write a spark code using boto3 library.
      * Upload any file to s3 and see whether we are gettng two cloudwatch, one for lambda and one for glue.
      
#### 11. Writing a glue shell job: [Link](https://github.com/kaustubh16-dev/Change-Data-Capture/tree/main/Images_and_Code/11.Writing%20a%20glue%20shell%20job) [code](https://github.com/kaustubh16-dev/Change-Data-Capture/blob/main/Images_and_Code/11.Writing%20a%20glue%20shell%20job/Glue.py)
      * Write the spark code for the CDC i.e insert, update and delete.
      * Create a S3 bucket for output.

#### 12. Full load pipeline:
      * Prerequisite: Delete everything from s3.
      * Start RDS and MDS instances.
      * Rds will invoke mysql and load the file into s3, MDS invoke the lambda function, it will invoke the glue job and we will get the output file in S3.
      * While starting the MDS make sure to restart not resume.
      * Change the data in the mysql database and see if you can get the new output file with the respective changes in S3.
      

