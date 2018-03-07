# UCL System Engineering Project - Peach
PEACH core architecture and generator
By Mengyang, Sandipan and Desislava
# Developer manual for Core Analytics Engine
This manual includes steps and tips for 
- Setting up the core architecture locally. 
- Deploying our architecture in a DC/OS system on Azure.

Hope it will be helpful for the next team working on Peach project.
### Core architecture on a local machine
- Preparation
Download the following packages on your local machine:
    1. NiFi (https://nifi.apache.org)
    2. Kafka (https://kafka.apache.org)
    3. Spark (http://spark.apache.org)
    4. Druid (http://druid.io)
    5. Metabase (http://www.metabase.com/start/docker.html) (Our suggestion is to use docker image)
    6. Zookeeper (http://zookeeper.apache.org)
    

- NiFi
    1. Run NiFi on your machine
    Change to the nifi directory and input command: (It usually takes minutes to start)
        ```sh
        $ bin/nifi.sh run
        ```
    2. Access NiFi GUI via http://localhost:8080/nifi/
    3. Import the template from /core-analytics-deliverables/NiFi-Kafka/
    4. Edit the configuration of the NiFi processors. For instance, modify the path to get and save files corresponding to your local system.
    5. Right click on the processor to start or stop the process.
    
    
- NiFi to Kafka
    1. Start Zookeeper
        Change to the zookeeper folder and input command
        ```sh
        $ cp conf/zoo_sample.cfg conf/zoo.cfg
        $ ./bin/zkServer.sh start
        ```
    2. Start Kafka
        Change to the kafka folder and input command
        ```sh
        $ bin/kafka-server-start.sh config/server.properties
        ```
    3. Start the NiFi processors for transmitting data to Kafka
    
    
- Kafka to Spark
    1. Copy the path to the spark job "sparkCS.jar" in  folder /core-analytics-deliverables/Kafka-Spark/
    2. Submit the job to Spark
        ```sh
        $ zip -d Path/To/SparkCS.jar META-INF/*.RSA META-INF/*.DSA META-INF/*.SF
        $ ./bin/spark-submit   --class com.ipponusa.SparkConsumer   --master local[8]   Path/To/SparkCS.jar
        ```
    3. You can access http://localhost:4040 to check the status.
    
    
- Druid and Metabase
    1. Doc for starting Druid and importing data from Kafka
    http://druid.io/docs/0.10.0/tutorials/quickstart.html
    http://druid.io/docs/0.10.0/tutorials/tutorial-kafka.html
    2. Run Metabase
        ```sh
        $ docker run -d -p 3000:3000 --name metabase metabase/metabase
        ```

### Deployment on Azure
- Preparation
    1. An Azure account with subscription
    2. ssh key pairs
    3. email addresses of team members

- Deploy DC/OS on Azure
    1. Login Azure portal
    2. Search for DC/OS service in the search bar
    3. Set up the deployment configuration in the following way
        ![alt tag](https://github.com/wumengyangok/Peach-CoreAnalytics-Generator/blob/master/project-website/img/azure_1.jpg)
        ![alt tag](https://github.com/wumengyangok/Peach-CoreAnalytics-Generator/blob/master/project-website/img/azure_2.jpg)
        ![alt tag](https://github.com/wumengyangok/Peach-CoreAnalytics-Generator/blob/master/project-website/img/azure_3.jpg)
    4. Once the deployment finished, you can access the system via ssh, with detailed steps in DC/OS documentation. (https://dcos.io/docs/1.9/installing/cloud/azure/)

- Install Kafka, Spark and NiFi
    1. After accessing the DC/OS system, you can install Kafka, Spark and NiFi directly. The only issue is make sure the number of Kafka brokers is smaller than 3, which is the up limit for a 5-agent-system from our experience.

- Access NiFi GUI
    There is a bug in DC/OS NiFi package so that we cannot access NiFi GUI by the link provided. To upload a template, we use X11 forwarding instead.
    ```sh
    $ ssh -Y azureuser@dcosmaster2rya43rjj5vky.uksouth.cloudapp.azure.com -p 2200
    ```
    Install any browser you like in the dcos master and you can access the NiFi GUI as you did locally.
