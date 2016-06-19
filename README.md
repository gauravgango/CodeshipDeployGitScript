# CodeshipDeployGitScript
Method to deploy to remote server using Codeship and Git using git hooks 

## Some common steps

1. Setup your Git repository and link it with your GitHub repository
2. Setup Codeship with a project and link it with GitHub
3. Setup your local machine
	````sh
	mkdir development
	cd development
	git init
	git remote add origin yourorigin.git
	git fetch origin
	git checkout master
	````
4. Create a git bare repository on the remote system(your server)
	````sh
	mkdir barerepo
	cd barerepo
	git init --bare
	````
5. Inside the hooks folder create a **post-receive** file and provide it permission to execute
	````sh
	cd hooks
	touch post-receive
	chmod +x post-receive
	````
6. Remove the Codeship deployment key from your git hub repository.
	* Go to settings in your git hub repository
	* Go to deployment keys
	* Remove the Codeship deployment key
7. Add the public ssh key provided in general setting of Codeship project to the git hub account
	* Go to settings panel of your git hub account
	* Go to SSH and GPO keys
	* Add SSH key of Codeship project
8. Add the Codeship public ssh key to the server/remote. So that Codeship can access the server/remote
	````sh
	cd ~/.ssh
	editor authorized_keys
	````
9. Create a deployment pipeline in Codeship using **Custom Script**
10. Add the content of **codeship-deploy** to the custom script
11. Create environment variables 
	* REMOTE_REPOSITORY = ssh user@yourdomain.com:/path/to/your/bare/repository
	* REMOTE_BRANCH = master


## Methods of deployment
1. Method 1 : On deployment checkout to new commit
2. Method 2 : On deployment pull the github origin

## Method 1 : On deployment by checking to new commit
1. Edit your post-receive with the content provided in the **Method1_post_receive** by providing the folder path of deploy to **--work-tree** and folder path of bare repository to **--work-dir**
2. The hook will be fired when Codeship will fire the deployment script
3. The **--work-tree** contains the path to your main folder where the code needs to be deployed
4. The **--git-dir** contains the folder where the bare repository was created. It is essentially the **.git** folder of your repository
5. When Codeship will deploy the content , it will be deployed to the bare repository. Then the bare repository will fire the post-receive script. It will check the branch and then checkout the contents of the deployment folder with the content received

## Method 2 : On deployment pull the github origin
1. Add the github origin to the deployment folder
	````sh
	git add origin yourgithuborigin.git
	````
1. Edit your post-receive with the content provided in the **Method2_post_receive**by providing the folder path of deploy to **--work-tree** and folder path of bare repository to **--work-dir** as the deploy folder's with **.git** folder
2. The hook will be fired when Codeship will fire the deployment script
3. The **--work-tree** contains the path to your main folder where the code needs to be deployed
4. The **--git-dir** contains the folder where the bare repository was created. Here it is the **.git** folder of your deployment folder as it contains the origin information
5. When Codeship fires its deployment it will fire the post-receive script but the script will fire **pull origin** command in that directory and pull the commit latest commit.

##NOTE
In method 1 no log status of commits will be available in the deployment folder i.e. git is not actively maintained there but in method 2 physical commits are being pulled down.

