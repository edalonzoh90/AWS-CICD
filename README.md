# AWS-CICD
Instructions to create a CICD pipeline in AWS

## CodeCommit
To upload your code in a CodeCommit repository.  

### Create new user
**From IAM/Users** - Create a new user  
**Username:** CICD-User  
**Options:**
  + [x] Access Key  
  + [x] Password  

**From the group tab** - Create a group  
**Group name:** Admin  
**Policies/policie name:** AdministratorAccess  

**Click on Create user and Download .csv**

### Upload the source code to CodeCommit

**From the CodeCommit option** - Create
### Generate git credentials
**From the new user detail** - Security credentials/Https Git Credentials/Generate credentials

### Upload the source code to CodeCommit  
**From the CodeCommit option** - Create repository  
**Name:** cicd-repo  

Using git commands, and the CICD-User credentials upload cicd-demo folder in the Code commit repository.

## CodeBuild  
**From Codebuild** - Create build project  
**Name:** DevOpsAppBuild  
**Source Provider:** AWS CodeCommit  
**Repository:** cicd-repo  
**Reference Type:** branch  
**Branch:** master  
**Environent:** Managed Image  
**Operating system:** Amazon Linux 2  
**Runtime:** Standard  
**Image:** Pick the last  
**Image Version:** Always use the latest image for this runtime version  
**Environment type:** Linux  
**Service role:** New service role  
**Build spec:** Use a buildspec file - (to use the buildspec.yml in the repo)  

**Click on Create Build Project**  
**Into the Build Project** - Start build  

### Artifacts  
To create artifacts from the build, following code was added to the bottom of buildspec.yml file  
from [aws documentation](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)

    artifacts:
      files:
        - '**/*'
      name: DevOpsAppArtifact

**From S3 Bucket** - Create new Bucket  
**Name:** cicddevopsartifacts  

**From DevOpsAppBuild project** - Edit Artifacts  
**Type:** Amazon S3  
**BucketName:** cicddevopsartifacts  
**NameSpace Type:** Build ID  
**Artifacts packaging:** Zip  
**Click on Update Artifact**  

**From DevOpsAppBuild project** - Start build  
After build ends, the new artifact should be added to cicddevopsartifacts bucket  

## CodeDeployment  
To do the deployment it's necesary to create the EC2 instances.  

**From IAM Dashboard/Roles** - Create Role  
**Common use case:** EC2  
**Policies:** AmazonS3ReadOnlyAccess   
**Role name:** EC2RoleForCodeDeploy  

**From EC2 Dashboard/Instances** - Launch instance  
**Name:** DevServer  
**Key Pair:** - Create new key pair  
**Key pair name:** cicd-rsa  
**Download the .pem file** 

**Advanced Details**  
**IAM instance profile:** EC2RoleForCodeDeploy  

## To connect to the EC2 Instance  
**From a linux terminal, with the ssh client installed** -  
ssh -i "path to the .pem file" ec2-user@IPv4-public-dns  

    ssh -i "CICD-DEPLOY.pem" ec2-user@ec2-35-170-197-215.compute-1.amazonaws.com
    
*** In case of getting a bad permissions error, run the following command:  
    
    chmod 400 CICD-DEPLOY.pem  

**From the ssh session, run following command to install the codedploy agent:**

    sudo yum update -y
    sudo yum install -y ruby wget
    wget https://aws-codedeploy-eu-west-1.s3.eu-west-1.amazonaws.com/latest/install
    chmod +x ./install
    sudo ./install auto
    sudo service codedeploy-agent status
  
## Deploying

It's necesary to setup an aws cli profile, create a new S3 bucket and enable versioning in it.  

**From Linux terminal**

    aws configure --profile aws-devops
    aws s3 mb s3://aws-devops-cicd-dev --region us-east-1 --profile aws-devops
    aws s3api put-bucket-versioning --bucket aws-devops-cicd-dev --versioning-configuration Status=Enabled --region us-east-1 --profile aws-devops

**Access key ID:** This info it's in the CICD-user_credentials.csv file  
**Secret access key:** This info it's in the CICD-user_credentials.csv file   
**Region:** us-east-1  
**Output:** json  

**From IAM Dashboard/Roles** - Create Role  
**Use case for other services:** CodeDeploy/CodeDeploy  
**Role name:** CodeDeployRole  

**From CodeDeploy** - Create application  
**Application name:** CodeDeployDev  
**Compute platform:** EC2/On-premises  

**From CodeDeployDev** - Create deployment group  
**Deployment group name:** DevInstances  
**Service Role:** DevInstances  
**Environments configuration:** Amazon EC2 instances  
**Key:** Name  
**Value:** DevServer  
**Load balancer:** Uncheck  

**From CodeDeployDev** - Create deployment  
**Deployment group:** DevInstances  
**Revision type:** My application is stored in Amazon S3
