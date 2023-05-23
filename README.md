# AWS-Deployment-Documentation
A comprehensive guide to setting up and deploying a scalable Golang web application with MongoDB on AWS.
Introduction

The objective of the project is to develop a scalable web application in Golang that can receive data from an external IoT data simulator. To streamline and expedite the application's deployment to production, we plan to implement Continuous Integration and Continuous Deployment (CI/CD) using AWS CodePipeline. Our primary goal is to automate the deployment process and minimize the time spent on manual deployments. We also want to ensure that the application can easily handle increasing traffic as user numbers grow. To accomplish this, we intend to leverage AWS services such as CodePipeline and CodeBuild for deployment automation, while relying on MongoDB as our database to securely and reliably store the IoT data. Our focus is on building a seamless and dependable pipeline for deployment and scalability, without utilizing Elastic Beanstalk.

## Chapter 1: Setting Up the Environment
   1- Creating an AWS account: You can sign up for an AWS account by visiting the official AWS website at https://aws.amazon.com/ and clicking on the "Create an AWS Account" button.

  2-  Installing the AWS CLI: For instructions on installing the AWS CLI, you can refer to the official AWS CLI User Guide at https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html

  3-  Creating an IAM user and assigning permissions: The AWS IAM User Guide provides detailed instructions on creating IAM users and managing their permissions. You can find it at https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html

  4-  Installing Golang: To install Golang, you can visit the official Golang website at https://golang.org/ and follow the installation instructions provided for your specific operating system.

  5-   Installing MongoDB: For instructions on installing MongoDB, you can refer to the MongoDB documentation at https://docs.mongodb.com/manual/installation/



## Chapter 2: Building the Simulator

The simulator is a valuable tool for IoT projects as it allows for testing and development without the need for physical devices. It offers the following benefits:

    Avoids the need to run physical equipment for unit or early integration testing.
    Enables the use of synthetic generated data or replayed captured data for testing purposes.
    Simulates a large number of devices by adding randomization.
    Allows data replay at different frequencies to expedite testing.
    Facilitates testing at different stages of the system, enabling the development and evaluation of various components independently.    How to install and use the simulator. 
    
link to simulator repo : https://github.com/IBA-Group-IT/IoT-data-simulator.
you just need to run the following commands in order to run the simulator: 
docker-compose pull
docker-compose up


## Chapter 3: Developing the Golang Application

    Overview of the application and its purpose
    Setting up a Golang project
    Designing the REST API
    Creating middleware for token validation
    Writing handlers for device creation and data storage

Chapter 4: Working with AWS Services

    Overview of AWS services used in the project (Elastic Beanstalk, CodePipeline, CodeBuild)
    Creating an Elastic Beanstalk environment
    Creating a CodePipeline for automated deployment
    Configuring CodeBuild to build the Golang application
    Adding MongoDB as an Elastic Beanstalk environment variable

Chapter 5: Scaling and Monitoring the Application

    Overview of scalability and monitoring
    
    
    Scaling Mongodb using Replicaset approach 
            +----------------+     +----------------+     +----------------+
        |    Primary     |     |    Secondary   |     |    Secondary   |
        |    Node 1      |     |    Node 2      |     |    Node 3      |
        |                |     |                |     |                |
        +-------^--------+     +-------^--------+     +-------^--------+
                |                      |                      |
                |                      |                      |
+---------------+----------------------+----------------------+
|                      Replica Set                             |
+--------------------------------------------------------------+

To scale our MongoDB infrastructure, we opted to use a ReplicaSet. A ReplicaSet consists of multiple nodes, including a primary node and one or more secondary nodes, that work together to provide data redundancy, high availability, and increased read scalability.

In our setup, we have two secondary nodes located in  eu-south-1a and eu-south-1b availability zones, along with a primary node. It is recommended to place these nodes within the same region to minimize network latency and ensure efficient data synchronization. Additionally, for improved fault tolerance, it is advisable to distribute the nodes across different availability zones within the region.
For our ReplicaSet implementation, we utilized three EC2 instances on the Amazon Ubuntu operating system, each equipped with 8GB of RAM. We ensured that the security group settings were consistent across all instances for seamless communication.

1- To install MongoDB on each instance, we followed these steps:
 We initiated an SSH connection to the EC2 instance using the command:
ssh -i "key.pem" [root@ec2ip.eu-south-1.compute.amazonaws.com](mailto:root@ec2ip.eu-south-1.compute.amazonaws.com)
2- Once connected, we edited the MongoDB repository file using the command:

sudo vi /etc/yum.repos.d/mongodb-org-4.4.repo
3- Inside the editor, we copied the following configuration:
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc

4- After saving the file, we proceeded to install MongoDB using the command:
sudo yum install -y mongodb-org
5- Next, we started the MongoDB service with the command:
sudo systemctl start mongod
6- To verify the successful installation, we checked the MongoDB status by running:
mongo
We repeated these installation steps on the other two EC2 instances to ensure consistency across the ReplicaSet.

Once the MongoDB instances were installed, we proceeded with setting up the ReplicaSet. In the primary instance, we executed the add.initialize() command, which initialized the ReplicaSet and designated the first instance as the primary node. This command kickstarts the process of electing the primary node and ensures data replication among the instances.

By following this installation and configuration process, we established a ReplicaSet with three EC2 instances running MongoDB in a primary-secondary configuration.


The primary node handles all write operations and serves as the source of truth for data updates. It replicates these updates to the secondary nodes, ensuring that they have an up-to-date copy of the data. The secondary nodes are available for read operations and can also step in as the primary in case of a failure or during planned maintenance.

To maintain data consistency and integrity, the ReplicaSet follows a consensus-based approach. This means that for any changes or updates to be considered valid, they must be replicated and acknowledged by a majority of the nodes within the ReplicaSet. This ensures that data remains consistent across the nodes, even in the event of failures or network partitions.

In case the primary node becomes unavailable due to a failure or maintenance, an automatic election process takes place. The remaining nodes participate in the election to select a new primary node. Once a new primary is elected, it resumes accepting write operations and continues replicating data to the secondary nodes.

By distributing the workload across the primary and secondary nodes, the ReplicaSet enables us to handle higher read traffic and provides fault tolerance. Additionally, the use of multiple secondary nodes allows for data redundancy and mitigates the risk of data loss.

Overall, our ReplicaSet architecture provides scalability, high availability, and data redundancy, making it a robust solution for our MongoDB infrastructure.


    Configuring Elastic Beanstalk to scale the application
    Using CloudWatch to monitor application performance
    Adding alarms to alert when thresholds are exceeded

Conclusion

    Recap of the project and its components
    Key takeaways from working with AWS services and Golang
    Suggestions for further development and improvement of the application.
