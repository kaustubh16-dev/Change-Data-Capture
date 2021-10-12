# Change Data Capture / Replication on going:


Detect the change automatically and reflect it in the output file without any human intervention.

Change data capture = In simple words, if we load all the data from rdbms like mysql to s3. if we add one row in mysql database it should automatically reflect on s3 is called change data capture.


## Steps Involved:
1. Create a data lake.
1. If there is any change in the data i.e. insertion, updation and deletion 
1. Take all those changes from the database, develop a pipeline which will consider  all the changes.

Step by step process:
1. Create an RDS instant. [RDS](https://github.com/kaustubh16-dev/Change-Data-Capture/blob/main/Steps/1Rds_instance.md)
2. Create a S3 bucket. [S3](https://github.com/kaustubh16-dev/Change-Data-Capture/blob/main/Steps/02_S3_Bucket.md)
3. Creating source and destination endpoint as well as replication instance [endpoint](https://github.com/kaustubh16-dev/Change-Data-Capture/blob/main/Steps/03_Source_endpoint_dest_endpoint.md)

    
