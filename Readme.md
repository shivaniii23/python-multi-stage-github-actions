We have a simple one file project to build a multi stage docker image.

Run the following commands to see the difference in the image sizes of 2 docker images-
docker build -t app .
docker build -t multi_stage -f Dockerfile_with_multi_stage .

**On-demand self-hosted AWS EC2 runner for GitHub Actions**

Use the following steps to prepare your workflow for running on your EC2 self-hosted runner:
1.	Create IAM user with AWS access keys
Create an IAM user with the required EC2 roles. To create, go to IAM -> Create user.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/3648fa0f-b0e3-45f8-bafd-3c468bead418)
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/ce77e45d-00ac-419b-b964-fdd3fd48d30d)

Then click on next ->  create.
Then we need to create the access key for this user. Click on the user -> Create access key .
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/d9fea834-d608-49d5-86ae-c29736d1081d)
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/52e438df-60d4-4092-a9f8-7cde59f3a225)
 
Click on next -> create access key.
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/e0b79758-b697-4027-b60f-f6420109f4bf)

Download the .csv file which will have the access key and the secret key.
Now, we will add these keys in our GitHub repository that we will use for this project.
Go to your repository on GitHub -> settings -> Secrets and variables -> Actions -> Secrets -> Repository secrets.
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/a4a828cd-d922-49b3-84ca-a988fab50210)

Add the secret key and access key from the csv we downloaded and the AWS_REGION.(eg - us-east-1). 

2.	Prepare GitHub personal access token
Create a new GitHub personal access token with the repository scope. The action will use the token for self-hosted runners’ management in the GitHub account on the repository level. Add the token to GitHub secrets.
Go to your GitHub profile on GitHub -> settings -> Developer Settings -> Personal Access Tokens -> Tokens(classic) -> Generate new token -> Generate new token(classic) -> Add note, set expiration date -> Select all the boxes in scope and -> Generate token.
You will get the token. Copy it because it won’t be accessible afterwards.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/54409118-bba1-457b-8e4e-cafadef5d364)

Now, we will add this token in our GitHub repository that we will use for this project.
We will also add docker hub credentials so that we can push the docker image.
Go to your repository on GitHub -> settings -> Secrets and variables -> Actions -> Secrets -> Repository secrets.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/07dc9da9-6b67-4fdd-bff3-16366886c946)

3.	Prepare EC2 image
Create a new EC2 instance based on any Linux distribution you need. Select a key-pair and new security group and create the instance.
Connect to the instance using SSH, install docker, then enable docker service –
            sudo apt-get update
            sudo apt install docker.io -y
            sudo systemctl enable --now docker
            sudo usermod -aG docker $USER && newgrp docker
            sudo systemctl restart docker
Install any other tools required for your workflow. Create a new EC2 image (AMI) from the instance.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/6b85f037-6e86-488b-a5c4-6c2bdb4157e2)

Select the instance -> Actions -> Image and templates -> Create image -> Enter the name and click on Create Image.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/45282270-3689-4f9b-a5d3-7b4c20ca9884)

Check the status of the image and make sure it is in ‘available’ state. Remove the instance if not required anymore.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/645a1464-3aae-4376-aa9d-41d384353e6b)

4.	Prepare VPC with subnet and security group
Create a new VPC and a new subnet in it. Or use the existing VPC and subnet. We will use the default VPC.
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/ad892420-307b-4e30-89be-69da11766b01)
 
Create a new security group for the runners in the VPC. Only the inbound traffic on port 22 is required. Keep the outbound traffic port as it is.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/227da693-d12b-4102-b7f9-5c6bf31ca297)
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/2b7127ac-3260-4846-a7b2-0fd646304c69)

5.	Configure the GitHub workflow
Create a new GitHub Actions workflow or edit the existing one. We have a project which creates a docker container with multi stage builds. 
Github repository - https://github.com/shivaniii23/python-multi-stage-github-actions.git
We have “deploy.yml” file which has the job named ‘My First GitHub Actions’ which starts the EC2 runner on push, executes the job, and terminates the runner after everything.
Make sure you don’t have a repository on the hub with the same name as this one.
 ![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/38b7da6f-2322-42ff-a571-38913c0e79b4)
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/0bf275b5-33e4-4829-a3c5-6189d157bd0e)
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/f4b9ab4b-7ec3-4f7e-8095-4f17a2f6bd1d)
![image](https://github.com/shivaniii23/python-multi-stage-github-actions/assets/54891990/b51d280f-e849-4e0a-b4ff-41bb1e0780cb)

Here, we can see that the docker image was built and pushed to docker hub.
