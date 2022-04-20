# AWS-CICD
Instructions to create a CICD pipeline in AWS

## CodeCommit
To upload your code in a CodeCommit repository.  

### Creating a new User  
From IAM/Users - Create a new user  
**Username**: CICD-User  
**Options**:  
  [x] Access Key  
  [x] Password   
:arrow_right:  
Create a group 
**Group name**: Admin
**Policies/Policie name:** AdministratorAccess
:arrow_right:  
:arrow_right:  
:arrow_right:  
**Create user** :arrow_right:  
:arrow_down:  **Download .csv**
--
Select the new CICD-User
Click on Security credentials/Https Git credentials/Generate credentials
--
Upload cicd-demo Folder in the Code commit repository using git
git clone [Repo URL]
