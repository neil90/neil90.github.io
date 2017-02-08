---
layout:     post
title:      AWS EMR with TPCDS/Jupyter
date:       2016-02-07
summary:    Launching EMR Cluster with TPCDS Data and Jupyter Notebook
categories: AWS
---

<p>Amazon has a pretty robust bootstrap script to install and setup Jupyter notebook located <a href="https://aws.amazon.com/blogs/big-data/running-jupyter-notebook-and-jupyterhub-on-amazon-emr/">here</a>. Script does everything I want however it seemed to add alot of time to the startup process and I really don't need all the other options. Currently I don't use any extra python packages when using Spark.</p>
<p>Another thing I wanted was having some fake data to mess around with benchmarking and what not with Spark and Tez Engine in EMR. Hortonworks created a mapreduce wrapper for <a href="https://github.com/hortonworks/hive-testbench">TPCDS data generator</a>.</p>
<p>In order to do this I just created 2 simple scripts that can be added as steps in launching aws emr cluster.</p> 

<p>TPCDS script is called <code>tpcds_load.sh</code> takes 2 parameters one for the size in GB to generate and the fileformat.</p>
<p>The Jupyter notebook setup script <code>simple_jupyter_spark_setup.sh</code> again takes 2 parameters password to sign into jupyter and the port to use.(note the web address is https)</p>

<p>I made them public so that anybody can use it. Here is an example of them being used -</p>

 <code>aws emr create-cluster --release-label emr-5.3.0 \
       --name 'emr-5.3.0 Spark Hive TPCDS Jupyter Setup' \
         --applications Name=Hadoop Name=Hive Name=Spark Name=Pig Name=Tez Name=Ganglia \
           --ec2-attributes KeyName=hdpcloud,InstanceProfile=EMR_EC2_DefaultRole,SubnetId=subnet-77b00100 \
             --service-role EMR_DefaultRole \
               --instance-groups \
                   InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge \
                       InstanceGroupType=CORE,InstanceCount=3,InstanceType=m3.xlarge \
                         --region us-west-2 \
                           --log-uri s3://aws-emr-neil-log/emr-logs/ \
                             --configurations '[{"Classification":"capacity-scheduler","Properties":{"yarn.scheduler.capacity.resource-calculator":"org.apache.hadoop.yarn.util.resource.DominantResourceCalculator"},"Configurations":[]}]' \
                               --steps \
                                 Type=CUSTOM_JAR,Name="LOAD TPCDS Data",ActionOnFailure=CONTINUE,Jar=s3://us-west-2.elasticmapreduce/libs/script-runner/script-runner.jar,Args=["s3://bootstrap-emr-neil/tpcds_load.sh","5","PARQUET"] \
                                   Type=CUSTOM_JAR,Name="Add Jupyter",ActionOnFailure=CONTINUE,Jar=s3://us-west-2.elasticmapreduce/libs/script-runner/script-runner.jar,Args=["s3://bootstrap-emr-neil/simple_jupyter_spark_setup.sh","zanzibar1","8882"]</code>


