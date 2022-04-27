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

**From the CodeCommit option** - Creat
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
