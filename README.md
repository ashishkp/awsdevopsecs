# Demo Project: AWS DevOps Services and Amazon Elastic Container Service

This Demo Project guides in implementing AWS DevOps Services using Blue-Green Deployment of Java and React based Monolith Application on Amazon ECS with Amazon EC2 & AWS Fargate launch type, one by one.

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About Demo Project](#about-demo-project)
  * [Built With](#built-with)
  * [Prerequisites](#prerequisites)
* [Setup Monolith Application](#setup-monolith-application)
  * [Install JHipster](#install-jhipster)
  * [Create Monolith Application](#create-monolith-application)
  * [Modify Application Demo for Trip Management usecase](#modify-application-demo-for-trip-management-usecase)
* [Setup AWS DevOps Process](#setup-aws-devops-process)
  * [Create required AWS IAM Roles](#create-required-aws-iam-roles)
  * [Create CodeCommit and Elastic Container Repository](#create-codecommit-and-elastic-container-repository)
  * [Setup CodeBuild with CodeCommit Integration](#setup-codebuild-with-codecommit-integration)
  * [Setup Amazon ECS with Autoscaling Amazon EC2](#setup-amazon-ecs-with-autoscaling-amazon-ec2)
  * [Setup CodeDeploy and CodePipeline with Amazon ECS on Amazon EC2](#setup-codedeploy-and-codepipeline-with-amazon-ecs-on-amazon-ec2)
  * [Modify CodeDeploy and CodePipeline for Amazon ECS on AWS Fargate](#modify-codedeploy-and-codepipeline-for-amazon-ecs-on-aws-fargate)
* [What's Next](#whats-next)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)

<!-- ABOUT Demo Project -->
## About Demo Project

This demo project was created as part of a `Master Builder` program. The `Master Builder Program (MBP)` is an internal SA-enablement initiative at AWS that aims to better prepare newly hired AWS Solution Architects for the type of work they'll be doing with our customers.

This demo implements proof of concept solution to a specific usecase of a fictitious company called Octank.

**Problem Statement**\
Octank's travel division wants to move their outdated online ticketing system, currently hosted on premises, to the cloud. They have following key current challenges.

1. They want their development and operations teams to adopt a DevOps model, and be able to take advantage of the speed and agility that AWS provides.
2. They want to be able to perform zero-downtime deployments, something they currently do not do, instead doing deployments in the middle of the night.
3. They have a distributed development team, and want a way to solve 'but it works on my machine' when dealing with distributed teams of developers.
4. They want to develop a mobile app that allows users to check on status of trips.

**Solution**\
Solution *proof of concept* demo is focusing on how Octank Team can leverage AWS cloud with **DevOps Services** and **Elastic Container Service** to run their existing code base - having multiple technologies like Java, React, NodeJs...etc\
\
![Project Demo Screenshot](images/project-demo-screenshot.png)

Solution is prepared with Amazon ECS service for deployment. It provides two launch type for deployment. Amazone EC2 and AWS Fargate. This demo shall cover both, and respective solution architecture diagrams are as given below.

##### AWS Demo Architecture - DevOps with ECS on EC2 launch type

![Project Demo Screenshot](images/prj-architecture-ecs-ec2.png)

##### AWS Demo Architecture - DevOps with ECS on Fargate launch type

In Architecture diagram, **green** marked section under VPC, shows difference w.r.t EC2 Launch type, in terms of services automatically taken care by AWS Fargate, making developer and operation team's job simple.

![Project Demo Screenshot](images/prj-architecture-ecs-fargate.png)

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

<!-- Setup Monolith Application -->
## Setup Monolith Application

We shall be using Jhipster Generator to generate code of our monolith application and modify homepage to personalize for use case of ficticious company Octank.

_About JHipster_ : Jhipster is an Apache-licensed open source project that allows you to generate Spring Boot APIs, as well as Angular or React or Vuejs UIs. It includes support for generating CRUD screens and adding all the necessary plumbing. It even generates microservice architectures!

### Install JHipster

```sh 
npm install -g generator-jhipster
```

### Create Monolith Application

* Let's create directory to put our application code and run JHipster and follow instructions on screen

    ```sh
    mkdir tripmgmt && cd tripmgmt
    jhipster
    ```
    ![Jhipster Init](images/jhipster-init.png)

* Give one by one answer to each configurable option questions and at the end complete application is generated with git repo initialization.

    ![Jhipster Init Complete ](images/jhipster-init-complete.png)

* Copy `references` folder from [awsdevopsecs][awsdevopsecs-github-url] Git repo to project folder.

    * move _Dockerfile_ and _entrypoint&#46;sh_ files from `references` folder to project root folder.

* Build application and create docker image.
    ```sh
    ./gradlew bootWar -Pprod -Pwar
    docker build -t tripmgmt .
    ```
* Test production release application locally through docker-compose.
    ```sh
    docker-compose -f src/main/docker/app.yml up 
    docker-compose -f src/main/docker/app.yml down # Once you are done with testing.
    ```
* Great ! You have successfully created Java and React based Monolith Application. If you want to customize it for demo, then follow along next section, else you can directly jump to AWS section for deploying through DevOps services.

* Note: Jhipster provides `jhipster aws` and `jhipster aws-containers` [generator options](https://www.jhipster.tech/aws/), which deploys to AWS ECS with Fargate launch type. However, it has limited support and all setup is done with cloudformation template. So, not a good option if you want to learn and explore AWS DevOps services and Amazon ECS with deep dive approach.

### Modify Application Demo for Trip Management usecase

1. Modify Jhipster boilerplate application to add Trips domain model. You can create your own data model through [JDL Studio](https://start.jhipster.tech/jdl-studio/) and import locally. For this demo, you can refer available trip.jh.

    ```sh
    cd <<PROJECT_ROOT>>
    jhipster import-jdl references\trip.jh # overwrite yes to all prompts
    ```

2. Copy **overwrite** provided `references\ui\favicon.ico` at `<<PROJECT_ROOT>>\src\main\webapp\`
3. Copy **overwrite** provided `references\ui\logo-jhipster.png` at `<<PROJECT_ROOT>>\src\main\webapp\content\images`
3. **Copy** provided `references\ui\octank_mb.png` at `<<PROJECT_ROOT>>\src\main\webapp\content\images` and **Edit** `<<PROJECT_ROOT>>\src\main\webapp\app\modules\home\home.scss` to replace `jhipster_family_member_2.svg` with `octank_mb.png`

4. **Edit** `<<PROJECT_ROOT>>\src\main\webapp\i18n\en\global.json` to  
	1. Update "title" to "Trip Management".
	2. Update "footer" to "© 2020, Octank, Inc. or its affiliates. All rights reserved."

5. Copy **overwrite** provided `references\ui\home.json` at `<<PROJECT_ROOT>>\src\main\webapp\i18n\en\`.
6. Copy **overwrite** provided `references\ui\home.tsx` at `<<PROJECT_ROOT>>\src\main\webapp\app\modules\home\`. 
7. Verify changes locally through steps mentioned in previous section.

## Setup AWS DevOps services

* Assume you have configured AWS CLI as given in pre-requisites. 
* Use single region (default region specified in AWS CLI configuration) where you would setup services in below applicable sections.

    *Note : This demo may require services which are not part of [AWS Free Tier](https://aws.amazon.com/free/)*

### Create required AWS IAM Roles

Check and create IAM roles, if it doesn't exist (in case, you have created for your earlier excercies, skip creating it again).

1. **Task Execution Role**: The Amazon ECS container agent make calls to the Amazon ECS API on your behalf, so it requires an IAM policy and role for the service to know that the agent belongs to you. This IAM role is referred to as a task execution IAM role.\
\
To create the `ecsTaskExecutionRole` IAM role

    1. Open the [IAM console](https://console.aws.amazon.com/iam/)
    2. In the navigation pane, choose **Roles, Create role.**
    3. In the **Select type of trusted entity** section, choose **Elastic Container Service**.
    4. For **Select your use case**, choose **Elastic Container Service Task**, then choose **Next: Permissions**.
    5. In the **Attach permissions policy** section, search for **AmazonECSTaskExecutionRolePolicy**, select the policy, and then choose **Next: Review**.
    6. For **Role Name**, type `ecsTaskExecutionRole` and choose **Create role**.

2. **ECS Instance Role**: This IAM role is required for the EC2 launch type. The Amazon ECS container agent makes calls to the Amazon ECS API on your behalf. Container instances that run the agent require an IAM policy and role for the service to know that the agent belongs to you.\
\
To create the `ecsInstanceRole` IAM role

    1. Open the [IAM console](https://console.aws.amazon.com/iam/)
    2. In the navigation pane, choose **Roles, Create role.**
    3. Choose the **AWS service** role type, and then choose **Elastic Container Service**.
    4. Choose the **EC2 Role for Elastic Container Service** use case and then **Next: Permissions**.
    5. In the **Attached permissions policy** section, select **AmazonEC2ContainerServiceforEC2Role** and then choose **Next: Review**.
    6. For **Role Name**, type `ecsInstanceRole` and choose **Create role**.

3. **ECS CodeDeploy Role**: Before you can use the CodeDeploy blue/green deployment type with Amazon ECS, the CodeDeploy service needs permissions to update your Amazon ECS service on your behalf. These permissions are provided by the CodeDeploy IAM role.\
\
To create the `ecsCodeDeployRole` IAM role

    1. Open the [IAM console](https://console.aws.amazon.com/iam/)
    2. In the navigation pane, choose **Roles, Create role.**
    3. Choose the **AWS service** role type, and then choose **CodeDeploy**.
    4. Choose the **CodeDeploy** use case and then **Next: Permissions**.
    5. For **Role Name**, type `ecsCodeDeployRole` and choose **Create role**.
    6. Open 'ecsCodeDeployRole` role again from [IAM console](https://console.aws.amazon.com/iam/), to add the required additional permissions.
    7. Choose **Attach policies**.
    8. To narrow the available policies to attach, for **Filter**, type **AWSCodeDeployRoleForECS**
    9. Check the box to the left of the **AWS managed policy** and choose **Attach policy** and **Update**.

4. **Service Linked Role for Amazon ECS**: Amazon ECS uses the service-linked role named **AWSServiceRoleForECS** to enable Amazon ECS to call AWS APIs on your behalf. The **AWSServiceRoleForECS** service-linked role trusts the `ecs.amazonaws.com` service principal to assume the role. Under most circumstances, this role should already exist, if not

    To create a service-linked role (CLI)
    ```sh
    aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com
    ```

5. **Service Linked Role for Amazone EC2 Auto Scaling**: Amazon EC2 Auto Scaling uses service-linked roles for the permissions that it requires to call other AWS services on your behalf. Amazon EC2 Auto Scaling uses the **AWSServiceRoleForAutoScaling** service-linked role. Under most circumstances, this role should already exist, if not

    To create a service-linked role (CLI)
    ```sh
    aws iam create-service-linked-role --aws-service-name autoscaling.amazonaws.com
    ```

### Create CodeCommit and Elastic Container Repository

**Create a CodeCommit repository**

1. Open the [CodeCommit console home](https://console.aws.amazon.com/codesuite/codecommit/).
2. In the region selector, choose the `default region`.
3. On the **Repositories** page, choose **Create repository**.
4. On the **Create repository** page, in **Repository name**, enter a name for the repository (i.e. `tripmgmtdemo`)

    *Note : Repository names are case sensitive. The name must be unique in the AWS Region for your AWS account.*
5. (Optional) In **Description**, enter a description for the repository. This can help you and other users identify the purpose of the repository.

6. (Optional) Select **Enable Amazon CodeGuru Reviewer for Java** if this repository will contain Java code, and you want CodeGuru Reviewer to analyze that Java code. CodeGuru Reviewer uses multiple machine learning models to find Java code defects and to automatically suggest improvements and fixes in pull requests. For more information, see the [Amazon CodeGuru Reviewer User Guide](https://docs.aws.amazon.com/codeguru/latest/reviewer-ug/Welcome.html).

    ![CodeCommit CodeGuru Option](images/codecommit-codeguru.png)

7. Choose **Create**.
8. Configure access to CodeCommit Repository from your local git client, make sure you have downloaded `HTTPS Git credentials for AWS CodeCommit` from [IAM console](https://console.aws.amazon.com/iam/) for your respective IAM User.

    ![CodeCommit IAM Credentials](images/codecommit-iam.png)

9. Add remote origin to project Git repo.

    ```sh
    git remote add origin <<HTTPS CodeCommit Repo URL>>
    git push origin master
    ````

**Create Elastic Container repository**

1. Open the [Amazon ECR console](https://console.aws.amazon.com/ecr/repositories).
2. From the navigation bar, choose the **Region** (i.e. `default region`) to create your repository in
3. In the navigation pane, choose **Repositories**.
4. On the **Repositories** page, choose **Create repository**.
5. For **Repository name**, enter a unique name for your repository (i.e. `devops/tripmgmtdemo`)
    
    ![ECR Create](images/ecr_create.png)

6. For **Tag immutability**, choose the tag mutability setting for the repository. Repositories configured with immutable tags will prevent image tags from being overwritten. For more information, see [Image Tag Mutability](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-tag-mutability.html).
7. For **Scan on push**, choose the image scanning setting for the repository. Repositories configured to scan on push will start an image scan whenever an image is pushed, otherwise image scans need to be started manually. For more information, see [Image Scanning](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning.html).
8. Choose **Create repository**.
9. Copy Repository URL to be used in subsequent sections.
	
### Setup CodeBuild with CodeCommit Integration
  
To setup CodeBuild, first we need to create/modify buildspec.yml file to describe build steps. After that we can configure CodeBuild Project linking with CodeCommit Repository.

![CodeCommit - CodeBuild](images/only-codecommit-build.png)

**Prepare `buildspec.yml` file**

1. Edit `buildspec.yml` file, located at `<<PROJECT_ROOT>>\references\buildspec.yml`
2. Replace <<YOUR_REPOSITORY_URI>> with your CodeCommit Repository URL - For example, *accountid*.dkr.ecr.*region*.amazonaws.com/*repository name*

**Setup CodeBuild Project**

1. Open the [CodeBuild console](https://console.aws.amazon.com/codesuite/codebuild/home).
2. On the **Build projects** page, choose **Create build project**.

    ![Create Build](images/codebuild-create.png)

3.  In **Project configuration**: Enter a name for this build project (i.e. `tripmgmt-demo-build`). Build project names must be unique across each AWS account. You can also include an optional description of the build project to help other users understand what this project is used for.\
If required, Select **Build badge** to make your project's build status visible and embeddable.

4. In **Source**: For **Source provider**, choose **AWS CodeCommit**. 
5. From **Repository**, choose the **repository** you have created for this project demo.
6. For **Reference type**, choose **Branch** and select *master* branch.
7. In **Environment**: choose **Managed Image** with **Operating system** as  *Amazon Linux 2*, 
8. For **Runtime(s)** choose *Standard* and **Image** choose the one with Standard:2.0 with **Image Version** as *Always use the latest image for this runtime version*
9. For **Environment type** choose *Linux* and Check mark **Privileged** action.
10. For **Service role** choose **New service role** and give meaningful name i.e. *codebuild-tripmgmt-demo-build-service-role*
11. Under **Additional configuration**, for **Compute** choose right spec EC2 instance type to run your build.
12. For **Buildspec name - optional**, add buildspec.yml file location - i.e. *references/buildspec.yml*
13. For **Logs**, check mark *CloudWatch logs* and give **Group name** as *codebuild-logs*, **Stream name** as *tripmgmtdemo-build*.
14. Click **Create build project**.
15. Now edit newly created service role  *codebuild-tripmgmt-demo-build-service-role* to allow accessing ECR repository through *AmazonEC2ContainerRegistryPowerUser* managed policy.
    1. Open 'codebuild-tripmgmt-demo-build-service-role` role from [IAM console](https://console.aws.amazon.com/iam/).
    2. Choose **Attach policies**.
    3. To narrow the available policies to attach, for **Filter**, type **AmazonEC2ContainerRegistryPowerUser**
    4. Check the box to the left of the **AWS managed policy** and choose **Attach policy** and **Update**.
16. Select the build project and **Start build** to test CodeCommit and CodeBuild integration. Post successful build, you can verify new docker image in ECR Repository console.

### Setup Amazon ECS with Autoscaling Amazon EC2

![Autoscaling Amazon EC2](images/only-ec2-autoscaling.png)

#### Create Your Application Load Balancer and Target Groups

In this section, you will create an Amazon EC2 application load balancer. You use the subnet names, and target group values you create with your load balancer later, when you create your Amazon ECS service.

The load balancer must use a VPC with two public subnets in different Availability Zones. In these steps, you confirm your default VPC, create a load balancer, and then create two target groups for your load balancer. 


**To create an Amazon EC2 application load balancer**

1. Sign in to the AWS Management Console and open the [Amazon EC2 console](https://console.aws.amazon.com/ec2/)
2. In the navigation pane, choose **Load Balancers**, choose **Create Load Balancer**.
3. Choose **Application Load Balancer**, and then choose **Create**.

    ![Create Application Load Balancer](images/alb-create.png)

4. In **Name**, enter the name of your load balancer i.e. `tripmgmtdemo-alb`
5. In **Scheme**, choose **internet-facing**.
6. In **IP address type**, choose **ipv4**.
7. Configure two listener ports for your load balancer:
    1. Under **Load Balancer Protocol**, choose **HTTP. Under Load Balancer Port**, enter `80`.
    2. Choose **Add listener**.
    3. Under **Load Balancer Protocol** for the second listener, choose **HTTP. Under Load Balancer Port**, enter `8080`.
8. Under **Availability Zones**, in **VPC**, choose the default VPC. Next, choose two default subnets you want to use. _**Make a note of subnet ids to use in later part of this tutorial**_
9. Choose **Next: Configure Security Settings**.
10. Choose **Next: Configure Security Groups**.
11. Choose **Create new security group**, 
    1. Allow `80` port (HTTP) inbound traffic from your IP
    2. Allow `8080` port (CustomTCPPort) inbound traffic from your IP 
12. Choose **Next: Configure Routing**.
13. In **Target group**, choose **New target group** and configure your first target group:
    1. In **Name**, enter a target group name (for example, `alb-tg-tripmgmtdemo-1` ).
    2. In **Target type**, choose **IP**.
    3. In **Protocol** choose **HTTP**. In **Port**, enter `80`.
    4. Choose **Next: Register Targets**.
14. Choose **Next: Review**, and then choose **Create**.

**To create a second target group for your load balancer**

1. After your load balancer is provisioned, open the Amazon EC2 console. In the navigation pane, choose **Target Groups**.
2. Choose **Create target group**.

    ![Create Target Group](images/alb-tg-create.png)

3. In **Name**, enter a target group name (for example, `alb-tg-tripmgmtdemo-2`).
4. In **Target type**, choose **IP**.
5. In **Protocol** choose **HTTP**. In **Port**, enter `8080`.
6. In **VPC**, choose the default VPC.
7. Choose **Create**.

    *Note: You must have two target groups created for your load balancer in order for your deployment to run. You only need to make a note of the ARN of your first target group. This ARN is used in the create-service JSON file in the next section.*

**To update your load balancer to include your second target group**

1. Open the Amazon EC2 console. In the navigation pane, choose **Load Balancers**.
2. Choose your load balancer, and then choose the **Listeners** tab. Choose the listener with port 8080, and then choose **Edit**.
3. Choose the pencil icon next to **Forward to**. Choose your second target group, and then choose the check mark. Choose **Update** to save the updates.

    ![Update second target group listener](images/alb-tg2-listener.png)


#### Create ECS Cluster with Autoscaling

Amazon ECS cluster auto scaling can be set up and configured using the AWS Management Console, AWS CLI, or Amazon ECS API. I will walk you through creating the resources for cluster auto scaling using the AWS Management Console to understand each service and parameter options in detail.

**Create an Amazon ECS cluster**

Use the following steps to create an Amazon ECS cluster. We will create an empty cluster so that we can manually create the Auto Scaling resources. When you use the AWS Management Console to create a non-empty cluster, Amazon ECS creates an AWS CloudFormation stack along with Auto Scaling resources. We want to avoid creating this AWS CloudFormation stack when using the cluster auto scaling feature.

1. Open the [Amazon ECS console](https://console.aws.amazon.com/ecs/).
2. On the navigation bar at the top of the screen, select `default region`.
3. In the navigation pane, choose **Clusters**.
4. On the **Clusters** page, choose **Create Cluster**.
5. For **Select cluster compatibility**, choose **EC2 Linux + Networking** and then choose **Next step**.

    ![ECS Cluster Create](images/ecs-cluster-create.png)

6. For **Cluster name**, enter `ecs-cluster-tripmgmtdemo` for the cluster name.
7. Select **Create an empty cluster** and then choose **Create**.


**Create the Auto Scaling Resources**

*Create an Auto Scaling launch configuration*

1. Open the [Amazon EC2 console](https://console.aws.amazon.com/ec2/).
2. On the navigation bar at the top of the screen, select `default region`.
3. On the navigation pane, under **Auto Scaling**, choose **Launch Configurations**.
4. On the next page, choose **Create launch configuration**.
5. On the **Choose AMI** page, search for and choose the latest `Amazon ECS-optimized Amazon Linux 2` AMI.

    ![ECS Optimized Image](images/ecs-optimized-image.png)

6. On the **Choose Instance Type** page, select `t3.2large`, then choose **Next: Configure details**. Here, `t3.2xlarge` selected as an example, you can choose EC2 Instance size as per your need.

7. On the **Configure details** page, do the following:
    1. For **Name**, enter `ASGLaunchConfig-TripmgmtDemo` for the launch configuration name.
    2. For **IAM role**, select already created container instance IAM role.
    3. Expand the **Advanced Details** section to specify user data for your Amazon ECS container instances.

        Paste the following script into the **User data** field. The `ecs-cluster-tripmgmtdemo` cluster was created in above step.

        ```sh
        #!/bin/bash
        echo ECS_CLUSTER=ecs-cluster-tripmgmtdemo >> /etc/ecs/ecs.config
        ```
    4. Choose **Skip to review**.
8. Choose **Create launch configuration**.

Next, create an Auto Scaling group using that launch configuration.

*To create an Auto Scaling group*

1. Open the [Amazon EC2 console](https://console.aws.amazon.com/ec2/).
2. On the navigation bar at the top of the screen, select `default region`.
3. On the navigation pane, under **Auto Scaling**, choose **Launch Configurations**.
4. On the next page, select the launch configuration we created in above step and choose **Create Auto Scaling group**.

    ![ASG Create](images/asg-create.png)

5. On the **Configure Auto Scaling group** details page, do the following:
    1. For **Group name**, enter `ASG-TripmgmtDemo` for the Auto Scaling group name.
    2. For **Group size**, enter 0. We will use Amazon ECS managed scaling so there is no need to have the Auto Scaling group launch any initial instances.
    3. For **Network**, choose a VPC for your Auto Scaling group.
    4. For **Subnet**, choose same subnets in your VPC which was selected for Application Load Balancer section.
    5. Expand the **Advanced Details** section. For **Instance Protection**, choose **Protect From Scale In**. This enables you to use managed termination protection for the instances in the Auto Scaling group, which prevents your container instances that contain tasks from being terminated during scale-in actions.

6. Choose **Next: Configure scaling policies**.
7. On the **Configure scaling policies** page, select **Keep this group at its initial size**. We will use Amazon ECS managed scaling so there is no need to create a scaling policy.
8. Choose **Review, Create Auto Scaling group**.
9. Repeat steps 3 to 8 to create a second Auto Scaling group but for **Group name** use `ASG-TripmgmtDemo-burst`.
10. Use the following steps to edit the max capacity value for each of your Auto Scaling groups.
    1. Choose **View your Auto Scaling groups**.
    2. Select your `ASG-TripmgmtDemo` scaling group. From the **Details** tab, choose **Edit**.
    3. For **Max**, enter `10`, then choose **Save**.
11. Repeat step 10 for your `ASG-TripmgmtDemo-burst` scaling group.

**Create a Capacity Provider**
		
1. Open the [Amazon ECS console](https://console.aws.amazon.com/ecs/).
2. On the navigation bar at the top of the screen, select `default region`.
3. In the navigation pane, choose **Clusters**.
4. On the **Clusters** page, select your `ecs-cluster-tripmgmtdemo` cluster.
5. On the **Capacity Providers** tab, choose **Create**.
6. On the **Create Capacity Providers** window, do the following.
    1. For **Capacity provider name**, enter `capprvdr-ecs-tripmgmtdemo` for the name.
    2. For **Auto Scaling group**, select the `ASG-TripmgmtDemo` Auto Scaling group you created.
    3. For **Managed scaling**, choose **Enabled**. This enables Amazon ECS to manage the scale-in and scale-out actions for the capacity provider.
    4. For **Target capacity %**, enter 100.
    5. For **Managed termination protection**, choose **Enabled**. This prevents your container instances that contain tasks and that are in the Auto Scaling group from being terminated during a scale-in action.
    6. Choose **Create**.

7. Choose **View in cluster** to see your new capacity provider.
8. Repeat steps 4 to 6, creating a second capacity provider with name `burst-capprvdr-ecs-tripmgmtdemo` with your `ASG-TripmgmtDemo-burst` Auto Scaling group.

**Set a Default Capacity Provider Strategy for the Cluster**

When running a task or creating a service, the Amazon ECS console uses the default capacity provider strategy for the cluster. The default capacity provider strategy can be defined by updating the cluster.

1. Open the [Amazon ECS console](https://console.aws.amazon.com/ecs/).
2. On the navigation bar at the top of the screen, select `default region`.
3. In the navigation pane, choose **Clusters**.
4. On the **Clusters** page, select your `ecs-cluster-tripmgmtdemo` cluster.
5. On the **Cluster : `ecs-cluster-tripmgmtdemo`** page, choose **Update Cluster**.
6. For **Default capacity provider strategy** choose, **Add provider**.
7. Select your `capprovider-ecs-tripmgmtdemo` capacity provider.
8. Choose **Add provider**, select your `burst-capprovider-ecs-tripmgmtdemo` capacity provider.
8. Leave the **Base** value at 0 and leave the **Weight** value at 1.
9. Choose **Update**. This will add the capacity providers to the default capacity provider strategy for the cluster.
10. Choose **View cluster**.
	  
### Setup CodeDeploy and CodePipeline with Amazon ECS on Amazon EC2

**Create Aurora RDS, with admin user and tripmgmt schema**

1. Sign in to the AWS Management Console and open the [Amazon RDS console](https://console.aws.amazon.com/rds/).
2. In the upper-right corner of the AWS Management Console, choose the `default region` in which we will create the DB cluster.
3. In the navigation pane, choose **Databases**.
4. Choose **Create database**.
5. In **Choose a database creation method**, choose **Standard Create**.
6. In **Engine options**, choose **Amazon Aurora**.

    ![Aurora RDS create](images/aurora-mysql-rds.png)

7. In **Edition**, choose *Amazon Aurora with MySQL compatibility*
8. Choose *One writer and multiple readers* in **Database features**
9. In **Templates**, choose *Dev/Test* template.
10. To enter your master password, do the following:
    1. In the **Settings** section, open **Credential Settings**.
    2. Clear the **Auto generate a password** check box.
    3. (Optional) Change the **Master username** value and enter the same password in **Master password** and **Confirm password**, for example (admin/mysql123)

11. For **DB instance size**, choose `db.t3.small`
12. For **Availability & durability**, choose *Create an Aurora Replica/Reader node in a different AZ (recommended for scaled availability)*
13. For **Connectivity**, defaults.
14. For **Additional configuration**
    1. **DB instance identifier**, enter *tripmgmt-demo-aurora*
    2. **Initial database name**, enter *tripmgmt*
    3. **Monitoring**, _uncheck_ Enable Enhanced monitoring

15. Choose **Create database**.
16. For **Databases**, choose the name of the new Aurora DB cluster.

    On the RDS console, the details for new DB cluster appear. The DB cluster and its DB instance have a status of **creating** until the DB cluster is ready to use. When the state changes to **available** for both, you can connect to the DB cluster. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB cluster is available.
              
17. On the **Connectivity & security** tab, note the port and the endpoint of the writer DB instance. Please make a note of the endpoint and port of the cluster.

**Prepare Task Definition and Create ECS Task**

A task definition is required to run Docker containers in Amazon Elastic Container Service. You can define multiple containers in a task definition. The parameters that you use depend on the launch type you choose for the task.

1. First create cloud watch log group, where taskdef is configured to send all logs.
    1. Go to [Cloudwatch Console](https://console.aws.amazon.com/cloudwatch/)
    2. Select **Log Groups**, 
    3. Click **Actions** dropdown and select **Create log group** 
    4. **Log Group Name:** enter `tripmgmt-demo-ecstask-loggrp`
2. Edit `taskdef.json` file, located at `<<PROJECT_ROOT>>\references\taskdef.json`
    1. Replace <<TASK_EXECUTION_ROLE_ARN>> with the one created in one of the above section.
    2. Replace <<LOG_GROUP>> with the one created above `tripmgmt-demo-ecstask-loggrp`
    3. Replace <<DEFAULT_REGION>> with the region in which you have created this demo resources
    4. Replace <<AURORA_MYSQL_RDS_URL>> with Aurora MySQL RDS Cluster URL.
    5. Replace <<DB_USERNAME>> with Database username for example `admin`
    6. Replace <<DB_PASSWORD>> with Database password for example `mysql123`. *Note: This is for dev/test purpose only, in production you can use AWS SSM service, with Spring Boot.*
    7. Replace <<ECR_LATEST_IMAGE_URL>> with Elastic Container Registry Image URL, it should be of the form __*accountid*.dkr.ecr.*region*.amazonaws.com/*repository name*:latest__
3. Create Taskdef from commandline

    ```sh
    aws ecs register-task-definition --cli-input-json file://taskdef.json
    ```
4. Capture the TaskDefinition ARN from output.
5. Commit `taskdef.json` in repository.
6. You can also verify Task in [Amazon ECS console](https://console.aws.amazon.com/ecs/), left navigation bar under **Amazon ECS**, **Task Definitions**.

**Prepare AppSpec File**

AppSpec file is a YAML-formatted file used by CodeDeploy to manage a deployment. 

1. Edit `appspec.yaml` file, located at `<<PROJECT_ROOT>>\references\appspec.yaml`
    1. Replace <<TASKDEF_ARN>> with previously created Task Definition, it should be of the form __arn:aws:ecs:*region*:*accountid*:task-definition/*taskname*:1__
    2. Replace <<CONTAINER_NAME>> with the actual container name - refer task definition file (i.e. `cntr-img-tripmgmt`)
2. Commit `appspec.yaml` in repository.   

**Prepare create-service.json and Create Service**

ECS Service, runs and maintains a desired number of tasks from a specified task definition. If the number of tasks running in a service drops below the desiredCount , Amazon ECS runs another copy of the task in the specified cluster. In addition to maintaining the desired count of tasks in your service, you can optionally run your service behind one or more load balancers. The load balancers distribute traffic across the tasks that are associated with the service.

1. Edit `create-service.json` file, located at `<<PROJECT_ROOT>>\references\create-service.json`
    1. Replace <<TASK_NAME>> with previously created Task Definition, it should be of the form __ *taskname*:1__ (i.e. `task-tripmgmt-demo:1`)
    2. Replace <<ECS_CLUSTER_NAME>> with the ECS Cluster created in one of the above section (i.e. `ecs-cluster-tripmgmtdemo`)
    3. Replace <<ALB_TARGET_GROUP_ARN>> with the ARN of already setup Application Load Balancer's first Target Group Listner.
    4. Replace <<CONTAINER_NAME>> with the actual container name - refer task definition file (i.e. `cntr-img-tripmgmt`)
    5. Replace <<MULTIPLE_SUBNET_IDS>> with the subnet-ids used in earlier sections for Application Load Balancer setup. It should be of the form `"subnet-xx..", "subnet-xx.."`
    6. Replace <<MULTIPLE_SECURITY_GROUPS>> with the Security Groups used in earlier sections. It should be of the form `"sg-xx..","sg-xx..","sg-xx.."`. These security groups should allow Task communication from ALB and to other services used (i.e. Aurora RDS).

2. Create Service from commandline, here in place of <<SERVICE_NAME>>, give unique name to the service.

    ```sh
    aws ecs create-service --service-name <<SERVICE_NAME>> --cli-input-json file://create-service.json
    ```
3. Commit `create-service.json` in repository.
4. You can verify Service created in [Amazon ECS console](https://console.aws.amazon.com/ecs/), left navigation bar under **Amazon ECS**, **Clusters** and select the cluster name, you can see recently created service, status of the service and tasks.

**Create Your CodeDeploy Application and Deployment Group (ECS Compute Platform)**
	
Create a CodeDeploy application and deployment group for the Amazon ECS compute platform, the application is used during a deployment to reference the correct deployment group, target groups, listeners, and traffic rerouting behavior.

*To create a CodeDeploy application*

1. Open the [CodeDeploy console](https://console.aws.amazon.com/codesuite/codedeploy/applications/) and choose **Create application**.
2. In **Application name**, enter the name you want to use (i.e. `Deploy-Tripmgmt-Demo-App`)
3. In **Compute platform**, choose **Amazon ECS**.
4. Choose **Create application**.

    ![CodeDeploy Application create](images/codedeploy-app-create.png)

*To create a CodeDeploy deployment group*

1. On your application page's **Deployment groups** tab, choose **Create deployment group**.
2. In **Deployment group name**, enter a name that describes the deployment group (i.e. `deploygrp-tripmgmt-demo`)
3. In **Service role**, choose a service role that grants CodeDeploy access to Amazon ECS.
4. In **Environment configuration**, choose your Amazon ECS cluster name and service name.
5. From **Load balancers**, choose the name of the load balancer that serves traffic to your Amazon ECS service.
6. From **Production listener port**, choose the port and protocol for the listener that serves production traﬃc to your Amazon ECS service.
7. From **Target group 1 name** and **Target group 2 name**, choose the target groups used to route traffic during your deployment. Make sure that these are the target groups you created for your load balancer.
8. Choose **Reroute traffic immediately** to determine how long after a successful deployment to reroute traffic to your updated Amazon ECS task.
9. Under **Original revision termination**, make it 0 - 0 - 0 for Days, Hours and Minutes. As we are testing, we can terminate original tasks immediately after rerouting traffic.
10. Choose **Create deployment group**.

*Create Your Pipeline*

In this section, you create a pipeline with the following actions:

A source stage with CodeCommit action

A build stage with CodeBuild action

A deployment stage with an Amazon ECS deploy action where the deployment runs with a CodeDeploy application and deployment group.

1. Sign in to the AWS Management Console and open the [CodePipeline console](http://console.aws.amazon.com/codesuite/codepipeline/home)
2. On the **Welcome** page, **Getting started** page, or the **Pipelines** page, choose **Create pipeline**.
3. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter `codepipeline-tripmgmt-demo`.
4. In **Service role**, Choose **New service role** to allow CodePipeline to create a new service role in IAM. For example, enter name as `codepipeline-tripmgmt-demo-service-role`

    ![Codepipeline settings](images/codepipeline-settings.png)

5. In **Artifact store**, Choose **Default location** to use the default artifact store, such as the Amazon S3 artifact bucket designated as the default, for your pipeline in the region you have selected for your pipeline.
6. Choose **Next**.
7. In **Step 2: Add source stage**, in **Source provider**, choose **AWS CodeCommit**. In **Repository name**, choose the name of the CodeCommit repository (i.e. `tripmgmtdemo`). In **Branch name**, choose the name of the branch that contains your latest code update (i.e. `master`)

    ![Codepipeline Source stage](images/codepipeline-source-stage.png)

8. Choose **Next**.
9. In **Step 3: Add build stage**, in **Build provider**, choose **AWS CodeBuild**. In **Project name** choose the name of the build project. 

    ![Codepipeline Build stage](images/codepipeline-build-stage.png)

10. Choose **Next**.
11. Choose **Skip deploy stage** and let first build complete.
12. **Review**, review the information, and then choose **Create pipeline**.

*Add Deploy Stage*

1. Open the [CodePipeline console](http://console.aws.amazon.com/codesuite/codepipeline/pipelines)
2. Choose `default region` from top section.
3. Select the Pipeline name, For example `codepipeline-tripmgmt-demo`.
4. Click **Edit**, and scroll down and "Add Stage", give **Stage name** as `BlueGreenDeployStage`.
5. Click **Add action group**, in **Action name** give action name (i.e. `Deploy-Tripmgmt-Demo-ECR`).
6. In **Action provider**, choose **Amazon ECS (Blue/Green)**.
7. In **Input artifacts**, choose **SourceArtifact**
8. In **AWS CodeDeploy application name**, choose CodeDeploy Application name. In **AWS CodeDeploy deployment group**, choose CodeDeploy Application's Deployment Group. 

    ![CodeDeploy Stage Action 1](images/codepipeline-deploy-action1.png)

9. In **Amazon ECS task definition**, choose **SourceArtifact** and enter `references/taskdef.json`
10. In **AWS CodeDeploy AppSpec file**, choose **SourceArtifact** and enter `references/appspec.yaml`

    ![CodeDeploy Stage Action 2](images/codepipeline-deploy-action2.png)

11. Choose **Done**
12. Choose **Save**
13. Test end to end CodePipeline through **Release change**. Post successful CodeCommit, CodeBuild and CodeDeploy Blue-Green deployment, CodePipeline dashboard and task replacement result in deployment should look like as below.

    ![CodePipline Release](images/codepipeline-release-dashboard.png)
    \
    \
    ![CodePipline Release](images/codepipeline-release-test.png)


### Modify CodeDeploy and CodePipeline for Amazon ECS on AWS Fargate

We now have working end-to-end DevOps pipeline with Blue Green Deployment using Amazon ECS with Amazon EC2 launch type. As we have seen in above section, one of the overhead using EC2 launch type is to manage EC2 fleet of servers.

Let's modify our pipeline to use AWS Fargate launch type in Amazon ECS cluster.

![Amazon ECS - AWS Fargate](images/only-ecs-fargate.png)

As per given architecture, portion highlighted in **Green** will be taken care by AWS Fargate. So, we don't require *Launch configuration*, *Autoscaling Groups*, *EC2 Instances* and *Capacity Providers for Autoscaling Groups*. 

**Stopping running Amazon ECS Service, which is using EC2 Launch Type**

1. Open [Amazon ECS console](https://console.aws.amazon.com/ecs/).
2. Left navigation bar under **Amazon ECS**, **Clusters** and select the cluster name.
3. Under **Services** tab, select the Service name to be removed and **Delete** it. Enter *delete me* under confirmation dialog box.
4. Tasks running under service, should be stopped. However, if it's not stopped, in 5-10 min, Go to **Tasks** tab, select all tasks and **Stop** them.
5. Optionally, you can also remove EC2 Launch type associated resources like *Launch configuration*, *Autoscaling Groups*, *EC2 Instances* and *Capacity Providers for Autoscaling Groups*.

**Launching Amazon ECS Service for AWS Fargate Launch type**

1. Edit `create-service.json` file, located at `<<PROJECT_ROOT>>\references\create-service.json`, with provided diff changes as given below.

    ```diff
    - "launchType": "EC2",
    + "launchType": "FARGATE",

    - "assignPublicIp": "DISABLED"
    + "assignPublicIp": "ENABLED"
    ```
2. Create Service from commandline, here in place of <<SERVICE_NAME>>, give unique name to the service, preferrbly even not used previously.

    ```sh
    aws ecs create-service --service-name <<SERVICE_NAME>> --cli-input-json file://create-service.json
    ```
3. Commit `create-service.json` in repository.
4. You can verify Service created in [Amazon ECS console](https://console.aws.amazon.com/ecs/), left navigation bar under **Amazon ECS**, **Clusters** and select the cluster name, you can see recently created service, status of the service and tasks.

    ![AWS Fargate Service](images/codepipeline-fargate-service.png)


**Adding newly created Amazon ECS Service in CodeDeploy Deployment Group**

1. Open the [CodeDeploy console](https://console.aws.amazon.com/codesuite/codedeploy/applications/) and choose deployed Application name.
2. Under **Deployment groups**, select Deployment Group name and **Edit** it.
3. Under **Environment configuration**, in **Choose an ECS service name**, select AWS Fargate launch type service created above.

    ![AWS Fargate CodeDeploy](images/codepipeline-fargate-codedeploy.png)

4. Under **Advanced - optional**, in **Rollbacks**, check mark **Disable rollbacks** as we are testing the deployment.
5. Choose **Save changes**

**Testing CodeDeploy and CodePipeline for Amazon ECS on AWS Fargate**

1. Open the [CodePipeline console](http://console.aws.amazon.com/codesuite/codepipeline/pipelines)
2. Choose `default region` from top section.
3. Select the Pipeline name, For example `codepipeline-tripmgmt-demo`.
4. Either modify, commit and push changes in CodeCommit Repository OR **Release change** to test the CodePipeline.
5. Once **Source stage** and **Build stage** completes and **Deploy stage** starts, click on **details**.
6. Blue Green deployment takes place, having first **Replacement tasks** getting created (*pending* state) with new deployment.

    ![AWS Fargate CodeDeploy](images/codepipeline-fargate-release1.png)
    \
    \
    ![AWS Fargate CodeDeploy](images/codepipeline-fargate-release2.png)

7. AWS Fargate completes deploying new deployment in newly created tasks.

    ![AWS Fargate CodeDeploy](images/codepipeline-fargate-release3.png)

7. As next step, traffic switch over from **Original tasks** to **Replacement tasks** and then **Original tasks** are terminated.

    ![AWS Fargate CodeDeploy](images/codepipeline-fargate-release4.png)
    \
    \
    ![AWS Fargate CodeDeploy](images/codepipeline-fargate-release5.png)

8. Hurray !!! You have successfully tested CodeDeploy and CodePipeline for Amazon ECS on AWS Fargate.

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