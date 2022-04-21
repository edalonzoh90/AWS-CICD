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
​
23
### Upload the source code to CodeCommit
24
**From the CodeCommit option** - Creat
### Generate git credentials
**From the new user detail** - Security credentials/Https Git Credentials/Generate credentials

### Upload the source code to CodeCommit
**From the CodeCommit option** - Create repository
**Name:** cicd-repo  

Using git commands, and the CICD-User credentials upload cicd-demo folder in the Code commit repository.

## CodeBuild
