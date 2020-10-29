# CDdemo
External repository for my Github Actions demo project
Continuous Deployment with Github Actions on Divio
Setting up a deployment service environment in Github for continuous deployment using Github Actions.
 
On Github
Create a new repository on Github,
On Divio control panel
In the Divio Control Panel, create a new project with the new github repository as your external repository 
Add the SSH Public key provided by Divio to your Github repository Settings Deploy Key and check the Allow write access to allow Divio to push in to the repository.
for your project and setup the necessary webhooks.
 
or open your existing project and get the project id from the control panel url  https://control.divio.com/control/<organisation-id>/edit/<project-id>/. 
On Github
Create a new repository on Github, go to Secrets from the Settings tab and add New secrets of your Divio access token and your project-id as DIVIO_API_TOKEN and DIVIO_PROJECT_ID environment variables respectively.
Open the Actions tab, you may also choose from the existing continuous deployment workflows, for this demo skip to set up a workflow yourself and a main.yml file will be created for you. You can rename it anything you want. 
Configure the yaml file as follows.
# This workflow will install Divio CLI and deploy a local project into the Divio cloud

name: Divio Continuous Deployment

# push event to master branch triggers the workflow
on:
 push:
   branches: [ master ]

# jobs run sequentially or in parallel to execute the workflow
jobs:

 # This workflow contains a single job called "deploy"
 deploy:
  # Set the environment variables of the secret keys for easy access
   env:
     DIVIO_API_TOKEN: ${{ secrets.DIVIO_API_TOKEN }}
     DIVIO_PROJECT_ID: ${{ secrets.DIVIO_PROJECT_ID }}  
     # DIVIO_PROJECT_SLUG: ${{ secrets.DIVIO_PROJECT_SLUG }}
  # The type of runner that the job will run on
   runs-on: ubuntu-latest
 # Steps represent a sequence of tasks that will be executed as part of the job
   steps:
   # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
   - uses: actions/checkout@v2
   # setup Python to be able to run pip to install Divio CLI
   - name: Set up Python 3.8
     uses: actions/setup-python@v1
     with:
       python-version: 3.8

   # - name: Deploy to Divio Cloud using the API
   #   run: |
   #     curl -X POST --data 'stage="$DIVIO_PROJECT_SLUG"' --header "Authorization: Basic "$DIVIO_API_TOKEN"" https://control.divio.com/api/v1/website/"$DIVIO_PROJECT_ID"/deploy/
  
   - name: Install Divio CLI
     run: |
       python -m pip install --upgrade pip
       pip install divio-cli
  
   # Login to Divio using the access token and deploy
   - name: Deploy to Divio
     run: |
       divio login "$DIVIO_API_TOKEN"
       divio project deploy --remote-id "$DIVIO_PROJECT_ID"
 


Commit the yaml file and clone the repository.
On the terminal
Set up your divio project locally (divio project setup project-slug).
Create a directory git initialize and clone your repository.
Change directory to the project and git remote add the github action repository.
git remote add main <github-action-repo>
Develop your applications and git push to github actions branch to automatically deploy your changes to Divio.
git push .
 
