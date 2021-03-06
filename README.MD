## AWS Logging & Monitoring Example Resources
The CloudFormation templates included in this repository are intended to assist you in configuring and installing Amazon CloudWatch and AWS X-Ray for on premises servers, Amazon EC2, Amazon ECS, and Amazon EKS. Templates are also provided for deploying ECS and EKS clusters and services with Amazon CloudWatch preconfigured.

## Resources Summary
The example resources are listed here by the use case that they are intended to support.  A summary is provided of how the resource can be used to support you in implementing the use case.

### Demo Resources
These resources are provided for you so that you can setup prerequisites and configuration files to demo the Amazon CloudWatch agent, Amazon X-Ray, and the Embedded Metric Format.

* [Sample VPC](./create_vpc_example.yaml) - This template can be used to deploy a sample Amazon VPC with a single public subnet.  It deploys an internet gateway, with a default route on the public subnet.  You need a VPC in order to deploy an Amazon EC2 instance.  The Amazon EC2 instance needs to access the public CloudWatch service endpoints.

### [Amazon ECS Example Resources](./examples/ecs)
This directory contains sample logging and monitoring resources for Amazon ECS.

* [AWS IAM Role for ECS Container Instances](./examples/ecs/ecs_cluster_role.yaml) - This CloudFormation template deploys an IAM role that includes AWS managed policies for ECS, CloudWatch, and AWS Systems Manager.  This enables you to capture logs and custom Amazon EC2 metrics on your ECS container instances as well as manage the underlying EC2 instances with AWS Systems Manager.  It also enables you to connect to the container instances with AWS Systems Manager Session Manager.

* [ECS Cluster CloudFormation Template with the SSM agent and CloudWatch agent installed - Linux](./examples/ecs/ecs_cluster_with_cloudwatch_linux.yaml) - This CloudFormation template is similar to the template deployed through the AWS ECS service console when you create a new ECS cluster.  It is modified to automatically install / update the AWS Systems Manager agent and Amazon CloudWatch agent.  It enables you to further customize operational components that you may want to install and configure as a standard for your ECS clusters.
 
* [Deploying the CloudWatch Agent to Collect EC2 Instance-Level Metrics on Amazon ECS](./examples/ecs/cwagent-ecs-instance-metric-cfn.yaml) - This CloudFormation template aligns to the manual deployment and setup of the Amazon CloudWatch agent for Amazon ECS Container Instances.  The template deploys a service to the Amazon ECS cluster specified for the Amazon CloudWatch agent and configures it to collect and report metrics to Amazon CloudWatch. The customized template reads its CloudWatch configuration from an SSM parameter named **ecs-cwagent-daemon-service** in the account and region.  You can use the sample CloudWatch agent configuration files to create the CloudWatch agent configuration and store them in the **ecs-cwagent-daemon-service** parameter.  [Refer to the AWS documentation for more details.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-ECS-instancelevel.html#deploy-container-insights-ECS-instancelevel-manual)

* [Firelens Example ECS Task Definition](./examples/ecs/ecs-task-nginx-firelense.json) - This json file provides an ECS task definition that can be used to demo FireLens with an nginx container.  You must update the **executionRoleArn** and **taskRoleArn** to a valid value within your account before use.

* [Amazon ECS Container example using EMF with CloudWatch to transmit custom metrics](./examples/ecs/ecs_cw_emf_example) - This directory contains a working example for a python application that transmits metrics to the Amazon CloudWatch service in EMF format using the CloudWatch agent.  It includes a Dockerfile to create the container image and a task definition file to deploy it to ECS.  You must update the task definition for your environment.  Update the **image** parameter to the ECR repository where you deploy the image.  Update the **executionRoleArn** and **taskRoleArn** to your account values.

* [Amazon ECS Container example using EMF with Firelens to transmit custom metrics](./examples/ecs/ecs_firelense_emf_example) - This directory contains a working example for a python application that transmits metrics to the Amazon CloudWatch service in EMF format using the Firelens agent.  It includes a Dockerfile to create the container image and a task definition file to deploy it to ECS.  You must update the task definition for your environment.  Update the **image** parameter to the ECR repository where you deploy the image.  Update the **executionRoleArn** and **taskRoleArn** to your account values.

### [Amazon EKS Resources](./examples/eks)
This directory contains sample logging and monitoring resources for Amazon EKS.

* [Amazon EKS Self-Managed Node Group](./examples/eks/amazon-eks-nodegroup.yaml) - This CloudFormation template creates a self-managed EKS node group for your EKS clusters.  The template adds permissions to the IAM role used by the underlying EKS nodes to includes policies for Amazon CloudWatch and AWS Systems Manager.  The template installs / updates the AWS Systems Manager agent for the EKS node.  The Amazon CloudWatch agent can be deployed to the EKS cluster as a [DaemonSet](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-metrics.html).  You can use this template to incorporate additional Amazon EC2 management components and configuration as necessary by your organization.  

* [Amazon EKS Launch Template for Managed Node Groups](./examples/eks/amazon-eks-nodegroup.yaml) - This CloudFormation template creates an Amazon EC2 Auto Scaling launch template that can be used with managed node groups.  The template installs / updates the AWS Systems Manager agent for the EKS node.  The Amazon CloudWatch agent can be deployed to the EKS cluster as a [DaemonSet](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-metrics.html).   You can use this template to incorporate additional Amazon EC2 management components and configuration as necessary by your organization.

* [Sample Prometheus JMX example for EKS with Amazon CloudWatch](./examples/eks/prometheus_jmx) - This directory contains a working example for collecting prometheus metrics from a tomcat and nginx server via the Amazon CloudWatch agent.  See the [documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights-Prometheus.html) for more details. 

### Amazon CloudWatch Agent Deployment & Management for Amazon EC2
* [Amazon S3 bucket for centralized CloudWatch agent configuration storage accessible by AWS Organization accounts](./cloudwatch-config-s3-bucket.yaml) - This CloudFormation template deploys an Amazon S3 bucket that can be made accessible to an entire AWS Organization or Organization Unit.  You can use this sample S3 bucket CloudFormation template to create an S3 bucket accessible by multiple accounts.  The template includes a parameter named OrganizationID that you can use to grant read access to all accounts within an AWS organization.  

    Once you have created the S3 bucket, you can create a key / folder prefix structure to store your CloudWatch configuration files.  You can use a folder structure such as this:
    
    * */config/standard/windows/ec2*:  You can store your standard Windows CloudWatch platform configuration file here for EC2.  You may further categorize your standard platform configurations for different windows versions, EC2 instance types, and environments. 
    * */config/standard/windows/onpremises*:  You can store your standard Windows CloudWatch platform configuration file here for on premises servers.  You may further categorize your standard platform configurations for different windows versions, EC2 instance types, and environments.
    * */config/standard/linux/ec2*:  You can store your standard Linux CloudWatch platform configuration file here for EC2.  You may further categorize your standard platform configuration for different linux distributions, EC2 instance types, and environments. 
    * */config/standard/linux/onpremises*:  You can store your standard Linux CloudWatch platform configuration file here for on premises servers.  You may further categorize your standard platform configuration for different linux distributions, EC2 instance types, and environments. 
    * */config/<application_name>*:  You can store your application specific CloudWatch configuration files here.  You may further categorize your applications with additional folders / prefixes for environments, etc
    
    This template is intended to be used by [AWS Systems Manager Setup with AWS CloudFormation](./SSMSetup.yaml) and [AWS Systems Manager Document to install /update the Amazon CloudWatch agent - configure from central S3 bucket](./SSMQuickSetupCloudWatchAgentInstallAndConfigure.yaml)

* [IAM Instance Profile for Amazon CloudWatch and AWS Systems Manager](./ssm-cloudwatch-instance-role.yaml) - This instance profile and role includes the AWS managed policy named **AmazonSSMManagedInstanceCore** for Systems Manager as well as the **CloudWatchAgentServerPolicy** for CloudWatch.  This can be deployed at a StackSet to each account.  Since IAM resources are global, it should only be deployed to a single region even in a multi-region configuration.
    
* [AWS Systems Manager Setup with AWS CloudFormation](./SSMSetup.yaml) - This CloudFormation template can be used to setup and customize the standard [AWS Systems Manager Quick Setup process](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-quick-setup.html).  This template includes the following major changes from the AWS Systems Manager setup for Organizations template to provide more flexibility for multi-account, multi-region deployment and custom CloudWatch configurations:
                                                                                                                                                                                                                                                                                                
    * Creates a customized AWS Systems Manager document with steps for Windows and Linux to download the CloudWatch configuration files from an S3 bucket to the CloudWatch configuration directory and then start / stop the agent in order to apply them.
                                                                                                                                                                                                                                                                                                
    The standard quick setup process includes options to install, configure, and update the CloudWatch agent with AWS Systems Manager Distributor and AWS Systems Manager State Manager.
        
    The following key actions are performed by SSM quick setup for CloudWatch:
    
    1. **Creation of Systems Manager Custom Documents**:  Quick setup creates the following SSM documents for use with AWS Systems Manager State Manager:
        * **CloudWatchAgentInstallAndConfigure**:  This SSM document will install the CloudWatch agent using AWS Systems Manager Distributor and will then configure the instance with a default CloudWatch agent configuration once.
        *. **CloudWatchAgentUpdate**:  This SSM document will uninstall and install the CloudWatch agent.  Uninstalling the agent doesn’t remove any CloudWatch configuration files you may have installed.
    2. **Creation of AWS Systems Manager State Manager associations**:  State manager associations are created to apply the custom Systems Manager documents:
        * **SystemAssociationForInstallAndConfigureCloudWatchAgent**:  This association runs the **CloudWatchAgentInstallAndConfigure** SSM document once.
        * **SystemAssociationForUpdateCloudWatchAgent**:  This association runs the **CloudWatchAgentUpdate** SSM document every 30 days. 
    3. Creation of the **AmazonSSMRoleForInstancesQuickSetup**:  This role is intended for use by EC2 instances to enable the AWS Systems Manager agent running on EC2 instances to communicate with the AWS Systems Manager service.  This role includes the **AmazonSSMManagedInstanceCore** managed policy for SSM but **does not** provide the permissions required for the CloudWatch agent running on EC2 to communicate with the CloudWatch service.  The permissions for the created **AmazonSSMRoleForInstancesQuickSetup** must be updated to include permissions to CloudWatch.  
    
* [AWS Systems Manager Document to install /update the Amazon CloudWatch agent - configure from central S3 bucket](./SSMQuickSetupCloudWatchAgentInstallAndConfigure.yaml) - This CloudFormation template creates a customized AWS Systems Manager document with steps for Windows and Linux to install and update the Amazon CloudWatch agent.  It also downloads CloudWatch agent configuration files from an S3 bucket accessible by AWS Organizations accounts to the CloudWatch configuration directory and then start / stop the agent on the instance in order to apply configuration changes.  It can be deployed as a StackSet to multiple accounts and regions to centrally maintain and update your CloudWatch agent management.

* [AWS Systems Manager Document to install /update the Amazon CloudWatch agent - configure from SSM Parameter Store](./ssm_single_region_single_account.yaml) - This CloudFormation template creates a customized AWS Systems Manager document with steps for Windows and Linux to install and update the Amazon CloudWatch agent.  It downloads the CloudWatch agent configuration from AWS Systems Manager Parameter Store parameters (AmazonCloudWatch-windows / AmazonCloudWatch-linux).  It may be more appropriate for single account, single region solutions because you must maintain the Amazon CloudWatch configuration data in parameter store in each region.  

### Sample CloudWatch Agent Configuration Files
These [CloudWatch agent json configuration files](./cloudwatch_agent_configs) provide the CloudWatch agent predefined metric sets.  You can use them as a starting point for custom CloudWatch agent configuration.


