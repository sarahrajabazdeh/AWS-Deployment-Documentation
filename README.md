# AWS-Deployment-Documentation
A comprehensive guide to setting up and deploying a scalable Golang web application with MongoDB on AWS.
Introduction

    Brief description of the project and its purpose
    Overview of the technologies used (Golang, AWS services, MongoDB)

Chapter 1: Setting Up the Environment

    Creating an AWS account
    Installing the AWS CLI
    Creating an IAM user and assigning permissions
    Installing Golang and MongoDB locally

Chapter 2: Building the Simulator

    Overview of the simulator and its purpose
    How to install and use the simulator
    Customizing data generation with JavaScript functions
    Configuring the simulator to send data to AWS services

Chapter 3: Developing the Golang Application

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

for example in this diagram, the replica set consists of three MongoDB nodes, where one of the nodes is the primary node, and the other two are secondary nodes. The primary node is responsible for handling all write operations and replicating the changes to the secondary nodes. The secondary nodes receive and replicate the changes made on the primary node to maintain data redundancy and high availability.

In case the primary node fails, one of the secondary nodes is automatically elected as the new primary node, and the replica set continues to function without interruption. The election process is based on a voting mechanism, where each replica set member has a vote, and the member with the most votes becomes the new primary node.

Overall, a replica set provides a way to distribute data across multiple MongoDB nodes, enabling automatic failover and maintaining data redundancy to ensure high availability and data durability.


    Configuring Elastic Beanstalk to scale the application
    Using CloudWatch to monitor application performance
    Adding alarms to alert when thresholds are exceeded

Conclusion

    Recap of the project and its components
    Key takeaways from working with AWS services and Golang
    Suggestions for further development and improvement of the application.
