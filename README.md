# Automation-with-CloudFormation
In this task I am gona use AWS Cloud Formation to deploy AWS services to validate IaaC using Yaml codes via CF AWS. 

Deploying infrastructure in a consistent, reliable manner is difficult — it requires people to follow documented procedures without taking any undocumented shortcuts. Plus, it can be difficult to deploy infrastructure out-of-hours when less staff are available. AWS CloudFormation changes this by defining infrastructure in a template that can be automatically deployed — even on an automated schedule.
This lab provides experience in deploying and editing CloudFormation stacks. It is an interactive experience, requiring you to consult documentation to discover how to define resources within a CloudFormation template.
The lab will demonstrate how to:
* Deploy an AWS CloudFormation stack with a defined Virtual Private Cloud (VPC), and Security Group.
* Configure an AWS CloudFormation stack with resources, such as an Amazon Simple Storage Solution (S3) bucket and Amazon Elastic Compute Cloud (EC2).
* Terminate an AWS CloudFormation and its respective resources.

Below are step by step procedures how can we perform this task. 

Open AWS Management console browser. 

# Step 1: Deploy a CloudFormation Stack

We are going to deploy a CloudFormation stack that creates a VPC as shown in this diagram:

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/5f311290-7152-4295-8be4-52f983258512)

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/cd60f6c8-885f-4686-87ce-1750235860ac)

As attached codes of Yaml task1,2,3 we have created three tasks. 
1. In the AWS Management Console, on the Services menu, click CloudFormation.

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/133b123c-0f1d-4d9b-bc1b-1d3336a28bf9)
   
2. Click **Create stack** then:
     * Click Upload a template file
     * Click Browse or Choose file and upload the template file you downloaded earlier
     * Click Next

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/b95ac6dd-82ea-4444-a7a8-19ad0a7fa856)

3. On the Specify Details page, configure:
     * Stack name: Lab
     _In the Parameters section, we will see that CloudFormation is prompting for the IP address ('CIDR') range for the VPC and Subnet. A default value has been specified by the template, so there is no need to modify these values._ 
4. Click Next
   _The Options page can be used to specify additional parameters. You can browse the page, but leave settings at their default values._
5.	Click Next
  _The Review page displays a summary of all settings. Some of the resources are defined with custom names, which can lead to naming conflicts. Therefore, CloudFormation prompts for an acknowledgement that custom names are being used._
6.	Click Create stack
  _The stack will now enter the CREATE_IN_PROGRESS status._
7.	Click the Events tab and scroll through the listing.
  _The listing shows (in reverse order) the activities performed by CloudFormation, such as starting to create a resource and then completing the resource creation. Any errors encountered during the creation of the stack will be listed in this tab._
8.	Click the Resources tab.
   _The listing shows the resources that are being created. CloudFormation determines the optimal order for resources to be created, such as creating the VPC before the subnet._
10.	Wait until the status changes to **CREATE_COMPLETE**. You can click Refresh occasionally to update the display.

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/374985fc-8e60-4410-aae0-97e456611b52)

        _Optional: Go to the VPC console to see the Lab VPC that was created. Then, return to the CloudFormation console._

# Step 2: Add an Amazon S3 Bucket to the Stack

So now we are going to edit CloudFormation template to add in S3 bucket and the we will update the stack with revised template. 
1. We will open Task1.yaml and include in it S3 bucket
   The template is like this,
   _MyS3Bucket:_
    _Type: AWS::S3::Bucket_

   P.s:_that we have added under the Resources: header in the template file_
2. Now we will click of Cloudformation entity in AWS management console , update & choose Replace current template, then choose Upload a template file. Click Choose file, then browse to and select the task1.yaml file that you modified.
3. click **NEXT** which will appear several times. 
    _Wait for CloudFormation to calculate the changes. Towards the bottom of the page, you should see something similar to this:_
   
    ![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/5d85942b-368b-492a-8126-b37f88e23d47)

    This indicates that CloudFormation will Add an Amazon S3 bucket.
4. Click Update stack
_After a minute, the stack status will change from UPDATE_IN_PROGRESS to UPDATE_COMPLETE._
5. Click the Resources tab.
_The bucket will now be displayed in the list of resources. CloudFormation will have assigned it a random name so that it does not conflict with any existing buckets._

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/1448a6d3-8f6a-47d7-8737-e43a28ba53e1)

# Step 3: Add an Amazon EC2 Instance to the Stack
Now our your objective is to add an Amazon EC2 instance to the template, then update the stack with the revised template.

        Whereas the bucket definition was rather simple (just two lines), defining an Amazon EC2 instance is more complex because it needs to use associated resources, such as an AMI, security group and subnet.
First, however, you will add a special parameter that is used to provide a value for the Amazon Machine Image (AMI).

1. Update the template by adding these lines in the Parameters section:

    _AmazonLinuxAMIID:_
    _Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>_
    _Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2_

     When writing CloudFormation templates, you can refer to other resources in the template by using the !Ref keyword. For example, here is a portion of the task1.yaml template that defines a VPC, then references the VPC within the Route Table definition:

_VPC:_
    _Type: AWS::EC2::VPC_
    _Properties:_
    _CidrBlock: 10.0.0.0/16_

  _PublicRouteTable:_
    _Type: AWS::EC2::RouteTable_
    _Properties:_
    _VpcId: !Ref VPC_

2. Use the tips below to update the template to add an Amazon EC2 instance with the following Properties:
       * ImageId: Refer to AmazonLinuxAMIID, which is the parameter added in the previous step
       * InstanceType: t3.micro
       * SecurityGroupIds: Refer to AppSecurityGroup, which is defined elsewhere in the template
       * SubnetId: Refer to PublicSubnet, which is defined elsewhere in the template
       * Tags: Use this YAML block:

_Tags:_
        _- Key: Name_
        _Value: App Server_
P.S:_When referring to SecurityGroupIds, the template is actually expecting a list of security groups. You therefore need to list the security group like this_

_SecurityGroupIds:_
        _- !Ref AppSecurityGroup_
3. Once you have edited the template, update the stack with your revised template file.

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/d35a2672-be6f-4475-af4f-45c2d001dc5a)

<img width="1118" alt="image" src="https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/bc8e6cbb-c566-4355-a8ba-4878621be397">

# Step 4: Delete the Stack

This is easy simply goto AWS Management Console select CloudFormation click **Delete** , then at the prompt, click Delete stack.
   The stack will show _**DELETE_IN_PROGRESS**_. After a few minutes, the stack will disappear.

_Note that , When a CloudFormation stack is deleted, CloudFormation will automatically delete the resources that it created. Verify that the Amazon S3 bucket, Amazon EC2 instance and the VPC have been deleted_

![image](https://github.com/fazalUllah-Khan/Automation-with-CloudFormation/assets/148821704/becf3fb1-f826-4945-bf6e-3d518a70ffad)

_The source of this task is from my learning during Generation-AWS Cloud support skill development_  
