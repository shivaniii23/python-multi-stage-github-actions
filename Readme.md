We have a simple one file project to build a multi stage docker image.

Run the following commands to see the difference in the image sizes of 2 docker images-
docker build -t app .
docker build -t multi_stage -f Dockerfile_with_multi_stage .

simple workflow file-
name: My First GitHub Actions
on: [push]
jobs:
  action-job:
    runs-on: self-hosted
    steps:
    - uses: actions/checkout@v1
    - name: Build and push image
      run: |
        docker build -t multi_stage -f Dockerfile_with_multi_stage .

**On-demand self-hosted AWS EC2 runner for GitHub Actions**

Use the following steps to prepare your workflow for running on your EC2 self-hosted runner:
1.	Prepare IAM user with AWS access keys
Create an IAM user with the required EC2 roles. To create, go to IAM -> Create user.
 
 
Then click on next ->  create.
Then we need to create the access key for this user. Click on the user -> Create access key .
 
 
Click on next -> create access key.
 
Download the .csv file which will have the access key and the secret key.
Now, we will add these keys in our GitHub repository that we will use for this project.
Go to your repository on GitHub -> settings -> Secrets and variables -> Actions -> Secrets -> Repository secrets.

 
Add the secret key and access key from the csv we downloaded and the AWS_REGION.(eg - us-east-1). 
2.	Prepare GitHub personal access token
Create a new GitHub personal access token with the repository scope. The action will use the token for self-hosted runners’ management in the GitHub account on the repository level. Add the token to GitHub secrets.
Go to your GitHub profile on GitHub -> settings -> Developer Settings -> Personal Access Tokens -> Tokens(classic) -> Generate new token -> Generate new token(classic) -> Add note, set expiration date -> Select all the boxes in scope and -> Generate token.
You will get the token. Copy it because it won’t be accessible afterwards.
 
Now, we will add this token in our GitHub repository that we will use for this project.
Go to your repository on GitHub -> settings -> Secrets and variables -> Actions -> Secrets -> Repository secrets.
 
3.	Prepare EC2 image
Create a new EC2 instance based on any Linux distribution you need. Select a key-pair and new security group and create the instance.
Connect to the instance using SSH, install docker, then enable docker service –
sudo apt-get update
sudo apt install docker.io -y
sudo systemctl enable --now docker
sudo usermod -aG docker $USER && newgrp docker
sudo systemctl restart docker
Install any other tools required for your workflow. Create a new EC2 image (AMI) from the instance.
 
Select the instance -> Actions -> Image and templates -> Create image -> Enter the name and click on Create Image.
 
Check the status of the image and make sure it is in ‘available’ state. Remove the instance if not required anymore.
 
4.	Prepare VPC with subnet and security group
Create a new VPC and a new subnet in it. Or use the existing VPC and subnet. We will use the default VPC.
 
Create a new security group for the runners in the VPC. Only the outbound traffic on port 443 should be allowed for pulling jobs from GitHub. And inbound traffic on port 22 is required.
 

 
5.	Configure the GitHub workflow
Create a new GitHub Actions workflow or edit the existing one. We have a project which creates a docker container with multi stage builds. 
Github repository - https://github.com/shivaniii23/python_multi_stage
We have deploy.yml file which has the job named ‘My First GitHub Actions’ which starts the EC2 runner on push, executes the job, and terminates the runner after everything.
 
 




