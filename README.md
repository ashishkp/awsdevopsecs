# ECS Reference Architecture: Continous Deployment Pipeline

This Demo Project guides in implementing AWS DevOps Services using Blue-Green Deployment of Java and React based Monolith Application on Amazon ECS with Amazon EC2 & AWS Fargate launch type, one by one.

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About Project](#about-project)
  * [Built With](#built-with)
  * [Prerequisites](#prerequisites)
* [Application Setup](appsetup.md#application-setup)
  * [Install JHipster](appsetup.md#install-jhipster)
  * [Create Monolith Application](appsetup.md#create-monolith-application)
  * [Modify Application Demo for Trip Management usecase](appsetup.md#modify-application-demo-for-trip-management-usecase)
* [Deployment Setup](deploysetup.md#deployment-setup)
  * [Create required AWS IAM Roles](deploysetup.md#create-required-aws-iam-roles)
  * [Create CodeCommit and Elastic Container Repository](deploysetup.md#create-codecommit-and-elastic-container-repository)
  * [Setup Aurora MySQL RDS Database](deploysetup.md#setup-aurora-mysql-rds-database)
  * [Setup Amazon ECS with Autoscaling Amazon EC2](deploysetup.md#setup-amazon-ecs-with-autoscaling-amazon-ec2)
* [DevOps Pipeline Setup](devopssetup.md#devops-pipeline-setup)
  * [Setup CodeBuild with CodeCommit Integration](devopssetup.md#setup-codebuild-with-codecommit-integration)
  * [Setup CodeDeploy and CodePipeline with Amazon ECS on Amazon EC2](devopssetup.md#setup-codedeploy-and-codepipeline-with-amazon-ecs-on-amazon-ec2)
  * [Modify CodeDeploy and CodePipeline for Amazon ECS on AWS Fargate](devopssetup.md#modify-codedeploy-and-codepipeline-for-amazon-ecs-on-aws-fargate)

* [What's Next](#whats-next)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)

<!-- ABOUT Project -->
## About Project

This demo implements proof of concept solution to a specific usecase of a fictitious company called Octank. 

**Problem Statement**\
Octank's travel division wants to move their outdated online ticketing system, currently hosted on premises, to the cloud. They have following key current challenges.

1. They want their development and operations teams to adopt a DevOps model, and be able to take advantage of the speed and agility that AWS provides.
2. They want to be able to perform zero-downtime deployments, something they currently do not do, instead doing deployments in the middle of the night.
3. They have a distributed development team, and want a way to solve 'but it works on my machine' when dealing with distributed teams of developers.
4. They want to develop a mobile app that allows users to check on status of trips.

**Solution**\
Solution *proof of concept* is focusing on how Octank Team can leverage AWS cloud with **DevOps Services** and **Elastic Container Service** to run their existing code base - having multiple technologies like Java, React, NodeJs...etc\
\
![Project Demo Screenshot](images/project-demo-screenshot.png)

Solution is prepared with Amazon ECS service for deployment. It provides two launch type for deployment. Amazone EC2 and AWS Fargate. This demo shall cover both, and respective solution architecture diagrams are as given below.

##### AWS Demo Architecture - DevOps with ECS on EC2 launch type

![DevOps with ECS on EC2 launch type](images/prj-architecture-ecs-ec2.png)

##### AWS Demo Architecture - DevOps with ECS on Fargate launch type

In Architecture diagram, **green** marked section under VPC, shows difference w.r.t EC2 Launch type, in terms of services automatically taken care by AWS Fargate, making developer and operation team's job simple.

![DevOps with ECS on Fargate launch type](images/prj-architecture-ecs-fargate.png)

### Built With
This section lists major frameworks and services part of working project demo.

* Monolith Application
    1. [JHipster Generator](https://www.jhipster.tech/)
    2. [Spring Boot](https://spring.io/projects/spring-boot)
    3. [React JS](https://reactjs.org/)
    4. [Bootstrap](https://getbootstrap.com/)
* AWS DevOps Services
    1. [AWS CodeCommit](https://aws.amazon.com/codecommit/)
    2. [AWS CodeBuild](https://aws.amazon.com/codebuild/)
    3. [AWS CodeDeploy](https://aws.amazon.com/codedeploy/)
    4. [AWS CodePipeline](https://aws.amazon.com/codepipeline/)

* AWS ECS related Services
    1. [Amazon Elastic Container Registry](https://aws.amazon.com/ecr/)
    2. [Amazon Elastic Container Service](https://aws.amazon.com/ecs/)
    3. [AWS Fargate](https://aws.amazon.com/fargate/)


### Prerequisites

You shall need to install below listed softwares/packages to setup, configure and run this demo project.

* Install Java 8+. I recommend you use [AdoptOpenJDK](https://openjdk.java.net/install/) builds, as they are open source and free
* Install Node.js from the [Node.js website](https://nodejs.org/en/download/) (please use an LTS 64-bit version, non-LTS versions are not supported)
* AWS [CLI](https://aws.amazon.com/cli/):  
    * [Configure AWS CLI](<https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html>
    )
        ```
        $ aws configure
        AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
        AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
        Default region name [None]: us-east-1
        Default output format [None]: json
        ```

## 1. [Application Setup](appsetup.md#application-setup)
 
## 2. [Deployment Setup](deploysetup.md#deployment-setup)
 
## 3. [DevOps Pipeline Setup](devopssetup.md#devops-pipeline-setup)

<!-- What's Next -->
## What's Next

Planning to add support for Amazon Elastic Kubernetes Service (EKS) - Managed Kubernetes Service. AWS has also recently announced support for [Amazon EKS on AWS Fargate](https://aws.amazon.com/blogs/aws/amazon-eks-on-aws-fargate-now-generally-available/), with this EKS and Fargate make it straightforward to run Kubernetes-based applications on AWS by removing the need to provision and manage infrastructure for pods, this shall also be super fun to implement.

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request


<!-- LICENSE -->
## License

Distributed under the Apache License. See `LICENSE` for more information.

<!-- CONTACT -->
## Contact

Ashish Patel - 
[![Twitter][twitter-shield]][twitter-url]
[![LinkedIn][linkedin-shield]][linkedin-url]
[![Github][github-shield]][github-url]

## Acknowledgements

* AWS IAM Roles
    1. [Amazon ECS Task Execution IAM Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)
    2. [Amazon ECS CodeDeploy IAM Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/codedeploy_IAM_role.html)
    3. [Amazon ECS Container Instance IAM Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html)
    4. [Service-Linked Role for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using-service-linked-roles.html)
    5. [Service-Linked Roles for Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/autoscaling-service-linked-role.html)
* [Create an AWS CodeCommit Repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-create-repository.html)
* [Creating Amazon ECR Repository](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-create.html)
* [Create a Build Project (Console) in CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/create-project.html#create-project-console)
* [Creating an Amazon Aurora DB Cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)
* [Tutorial: Create a Pipeline with an Amazon ECR Source and ECS-to-CodeDeploy Deployment](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-ecs-ecr-codedeploy.html)
* [Tutorial: Using Cluster Auto Scaling with the AWS Management Console](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/tutorial-cluster-auto-scaling-console.html)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)

<!-- MARKDOWN LINKS & IMAGES -->
[awsdevopsecs-github-url]: https://github.com/ashishkp/awsdevopsecs
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=flat-square&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/ashishkp
[twitter-shield]: https://img.shields.io/twitter/url/https/shields.io.svg?style=social
[twitter-url]: https://twitter.com/ashishkp4u
[github-shield]: https://img.shields.io/github/followers/ashishkp.svg?style=social
[github-url]: https://github.com/ashishkp