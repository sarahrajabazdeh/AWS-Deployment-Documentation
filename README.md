# AWS-Deployment-Documentation
A comprehensive guide to setting up and deploying a scalable Golang web application with MongoDB on AWS.
Introduction

The objective of the project is to develop a scalable web application in Golang that can receive data from an external IoT data simulator. To streamline and expedite the application's deployment to production, we plan to implement Continuous Integration and Continuous Deployment (CI/CD) using AWS CodePipeline. Our primary goal is to automate the deployment process and minimize the time spent on manual deployments.We also want to ensure that the application can easily handle increasing traffic as user numbers grow. To accomplish this, we intend to leverage AWS services such as CodePipeline and CodeBuild for deployment automation, while relying on MongoDB as our database to securely and reliably store the IoT data. Our focus is on building a seamless and dependable pipeline for deployment and scalability, without utilizing Elastic Beanstalk.

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

## Chapter 3: General Architecture
<a href="https://ibb.co/KN2MnnQ"><img src="https://i.ibb.co/q0Nt664/5.png" alt="5" border="0"></a>

1-  The end user initiates an HTTPS request to access our application.

2- The request is routed to an Application Load Balancer (ALB) in AWS. The ALB acts as the entry point for incoming requests and performs load balancing to distribute the requests among a group of target instances.

3-  We have defined a target group, which represents a logical collection of instances capable of handling requests. This target group is associated with Elastic Container Service (ECS).

4-  Within ECS, we have defined two tasks. A task is a blueprint for running containers. Each task represents an instance of our Golang web application.

5-   Our Golang web application is packaged and deployed as a Docker image. The Docker image is stored in Elastic Container Registry (ECR), which is a managed Docker container registry provided by AWS.

6-    ECS, using Fargate, runs the containers based on the defined tasks. Fargate is a serverless compute engine for containers, allowing us to deploy and manage containers without provisioning or managing the underlying infrastructure.

7-   The Golang web app containers deployed by ECS can now process the user's request. They can interact with other services, such as accessing data from MongoDB.

8-  MongoDB is hosted on EC2 instances, which are virtual servers provided by AWS. We have set up MongoDB instances as replicasets, which means they contain the same data and provide redundancy and high availability.

9- The MongoDB replicasets are distributed across three different availability zones (AZs), which are isolated locations within an AWS region. This distribution ensures that even if one AZ experiences an issue, the replicasets in other AZs can continue to serve data.

## Chapter 4: Developing the Golang Application

  Router:  An incoming request is received by the router, which determines the appropriate controller to handle the request based on the endpoint.

  Controller :The controller receives the request and extracts any necessary data from it. It then delegates the execution of the business logic to the corresponding service.

  Service:  The service layer performs operations on the data received from the controller. This can include interacting with the database to fetch or modify data and applying business rules or computations.

   If necessary, the service layer may interact with external services or APIs to complete its tasks.

   Once the service layer has processed the request, it returns the response to the controller.

   The controller prepares the response based on the result from the service layer and sends it back to the client as an HTTP response.
   Useful files or packages: 
   * Make File :
     - This Makefile provides a set of commands for building, testing, and running a Go application. Here's a summary of the available commands:
     Here's a summary of the available commands:

    help: Displays a list of available commands.
    run: Executes the Go application using go run.
    build: Builds the application into an executable file.
    trivy: Runs the Trivy tool to scan for possible vulnerabilities in the code.
    ci: Runs additional checks on the code, including semgrep, lint, and trivy.
    semgrep: Runs semgrep to check for vulnerabilities using specific configurations.
    lint: Runs the linter golangci-lint to check for code quality issues.
    prepare_test: Prepares the unit tests folder.
    test: Prepares the tests and runs unit tests.
    launch: Builds the Go app and launches Docker containers containing the app and MongoDB replica set.
    shutdown: Stops and cleans up the Docker containers running the Go app and MongoDB replica set.
    conf: Generates a configuration file.
    docs: Generates Swagger documentation for the API using swag init.

## Chapter 5: Working with AWS Services

   ECS (Elastic Container Service):
        Reason for using: We are using ECS to deploy our Golang web application as a containerized task. ECS helps us easily manage and orchestrate the deployment, scaling, and monitoring of our containers.

   Fargate:
        Reason for using: We are using Fargate as a compute engine for containers, allowing us to run containers without managing the underlying infrastructure. By using Fargate with ECS, we can focus on running our containers without the need to provision or manage EC2 instances.

   CodePipeline:
        Reason for using: We are using CodePipeline as a fully managed continuous integration and continuous delivery (CI/CD) service. It helps us automate the build, test, and deployment processes for our application. We use CodePipeline to set up a CI/CD pipeline for our Golang web application.

   ECR (Elastic Container Registry):
        Reason for using: We are using ECR as a fully managed container registry that allows us to store, manage, and deploy container images. We use ECR to push and store our container images, making them available for deployment with ECS.

   EC2 (Elastic Compute Cloud):
        Reason for using: We are using EC2 instances to set up a MongoDB replica set. EC2 provides virtual servers in the cloud, allowing us to run applications and services, in this case, MongoDB, on scalable and customizable virtual machines.

   CodeBuild:
        Reason for using: We are using CodeBuild as a fully managed build service that compiles our source code, runs tests, and produces software packages. We use CodeBuild as part of our CI/CD pipeline to build our Golang web application and generate the necessary artifacts for deployment.
Chapter 6: Scaling and Monitoring the Application
    
    
    Scaling Mongodb using Replicaset approach 

       Overview of scalability and monitoring

  Chapter 7: Scaling MongoDB using ReplicaSet approach
  
    +---------------------+     +---------------------+     +---------------------+
    |  mongo (eu-south-1c) |     |   rep1 (eu-south-1a) |     |   rep2 (eu-south-1b) |
    |   (Primary)         |     |   (Secondary)        |     |   (Secondary)        |
    |                     |     |                      |     |                      |
    +-----------^---------+     +-----------^----------+     +-----------^----------+
                |                            |                            |
                |                            |                            |
                +----------------------------|----------------------------+
                                             |
                                             |
                                     Replication and Sync

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
For adding new replica:
Connect to the primary node:

css

mongo --host <primary-node-hostname> --port <primary-node-port>

Authenticate with the appropriate user credentials, if necessary.

Add a replica node using the rs.add() command:

csharp

rs.add("<replica-node-hostname>:<replica-node-port>")

Replace <replica-node-hostname> and <replica-node-port> with the hostname and port of the replica node you want to add. Ensure that the replica node is accessible from the primary node.

Optionally, you can add more replica nodes using the same rs.add() command for each additional node.

Monitor the ReplicaSet status to verify the addition of the new replica nodes:

lua

rs.status()

This command provides information about the current ReplicaSet configuration, including the primary node, secondary nodes, and their statuses.

Confirm the successful addition of the replica nodes by checking the output of rs.status(). The new replicas should be listed as secondary nodes and actively replicating data from the primary.

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
