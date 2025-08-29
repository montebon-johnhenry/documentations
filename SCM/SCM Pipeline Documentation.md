# **SCM Pipeline:**

# ![Picture1](media/Picture1.png)

# OCI Pipeline Creation

## <u>***Project Folder Setup***</u>

### Data Integration -\> Projects: Create/Set up Project Folder 

# ![Picture2](media/Picture2.png)



## ***<u>Setup Tasks for Data loading</u>*** 

- ### Data Loader or Dataflow

  *Note: Moving forward, it is advised to use Dataflow for flexibility*

### Data Integration -\> Projects -\> Task 

# ![Picture3](media/Picture3.png)

#### *Two type of Task setup, Data Loader and Dataflow*

### Data Loader 

# ![Picture4](media/Picture4.png)

# ![Picture5](media/Picture5.png)

### Dataflow

#### *Source:* 

# ![Picture6](media/Picture6.png)

#### *Target:*

### ![Picture7](media/Picture7.png)



## <u>***Once set upped Data Loader/Flow Publish to Application***</u>

#### ![Picture8](media/Picture8.png)



## <u>***Setup Pipeline***</u>

### Home -\> Projects -\> Project Name -\> Pipelines 

# ![Picture9](media/Picture9.png)

#### *Organize Group Related Data* 

#### *Note: Group them based on the “Schema” from the Data Loading Set Up(Data Loader/Flow)* 

##### 		![Picture10](media/Picture10.png)		

##### 		![Picture11](media/Picture11.png)

# ![Picture12](media/Picture12.png)



## <u>***Scheduling Set up For Pipelines***</u>

### Home -\> Application -\> Default Application

![Picture13](media/Picture13.png)

### Find the pipeline that you created and select schedule on the option

![Picture14](media/Picture14.png)

### In schedule portion click select 

# ![Picture15](media/Picture15.png)

### Click create schedule, once done setting up schedule save it 

# ![Picture16](media/Picture16.png)

### Select the created schedule for the pipeline and click the select button below  

# ![Picture17](media/Picture17.png)

### Once done setting up, the pipeline will run the next day based on the time of the schedule that you chose . You can check the loaded object to the bucket (Based on target bucket from Data Loading setup)

# ![Picture17](media/Picture17.png)

# ![Picture19](media/Picture19.png)



## ***<u>Background Python Code</u>***

If changes made in the pipeline is made (new or updated object) it will trigger to copy those objects and put it in



# AWS Pipeline Creation

## <u>***Checking***</u>

- #### Once data object has been successfully imported to AWS Glue staging bucket s3://apc-fusion/oci_fusion_data/ 

- #### It will automatically move to Landing bucket s3://apc-datalake/landing/fusion/ 

- #### It is implemented this way for external visibility purposes

- #### You can now check the data in the landing bucket 

# ![Picture20](media/Picture20.png)

## <u>***Setup configuration using glue script***</u>

### This setup insures that you have a config in the parameter store for connection options, folders and project setup

*NOTE:*

- *ignored_files = list of objects in the Landing folder that would be excluded in the config*

- *files_to_process = list of file you need to process, empty string means all objects in the Landing folder will be process*

# ![Picture21](media/Picture21.png)

## <u>***Setup “Run Job” Script***</u>

NOTE:

- To add or ommit a data source edit the list below with appropriate DATASET, WRITE_MODE and FILE_TYPE

- Dataset name should be same in the dataset name in the parameter store  
  
  # ![Picture22](media/Picture22.png)

&nbsp;

- WRITE_MODE = refresh or increment

- FILE_TYPE = parquet or csv (source object file type)

  # ![Picture23](media/Picture23.png)

  Initially run the job manually and check if it is successful

&nbsp;

## <u>***Create a crawler***</u>

# ![Picture24](media/Picture24.png)

&nbsp;

- #### Add a data source

  # ![Picture25](media/Picture25.png)

&nbsp;

- #### Follow provided parameters and save

- #### Delta table paths should point to correct s3 path in the bronze layer  
  
  # ![Picture26](media/Picture26.png)
  
- #### Select the IAM role  
  
  # ![Picture27](media/Picture27.png)
  
- #### Select the database

  # ![Picture28](media/Picture28.png)

  NOTE: You can create a database by selecting this option  

  # ![Picture29](media/Picture29.png)

- #### Initially run the Crawler and check if no errors

- #### Create Workflow and schedule the job  
  
  # ![Picture30](media/Picture30.png)

&nbsp;

## <u>***After workflow is created, run it first and check if no issues***</u>  

# ![Picture31](media/Picture31.png)

# Common Issue(s) and Resolution(s)

- Data not updated on SCM Dashboard

  - Possible Cause

    - OCI Scheduling issue

      - Need to update or recreate the schedule

    - Possible no data at all

      - Try to run the OCI job manually and see if Object File storage in OCI is updated

    - AWS Glue job failure

      - You can check on the Job Monitoring in Glue and pinpoint the actual error if exist

    - File exceeds 25mb in OCI Object Storage

      - Since we have a limitation of file size when transferring from OCI Object Storage to AWS S3 if the file exceeds 25mb you need to change the Extraction Strategy in either Data Loader or Dataflow

        # ![Picture32](media/Picture32.png)
