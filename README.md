# CDdemo - External repository for Divio Continuous deployment with Github Actions

Setting up a deployment service environment for continuous deployment using Github Actions.
 
* Create a new repository on Github with a master branch.

* Create a new project on [Divio control panel](https://control.divio.com/control/) with the new github repository as your external git server 

* Add the SSH public key provided by Divio to your Github repository **Settings**, **Deploy Key** and check the **Allow write access** to allow Divio to push in to the repository.

* Configure Webhooks for the github to send signals when commits are made. See how to configure webhooks in [Divio Developer Handbook](https://docs.divio.com/en/latest/how-to/resources-configure-git/#configure-a-webhook-for-the-git-repository).

* Setup the Secret Keys to be able to login to Divio. In your github repository, go to **Secrets** from the **Settings** tab and add **New secrets** of your [Divio API access token](https://control.divio.com/account/desktop-app/access-token/) as `DIVIO_API_TOKEN` and the **environments uuid**s as `TEST_ENVIRONMENT_ID` and `LIVE_ENVIRONMENT_ID` environment variables.

You maybe able to get the environments' uuids in many different ways, for example, using `curl`: From the control panel, you can get the `project-slug` and the `API access token`. 

To get the `project(application)-uuid`:

  `curl https://api.divio.com/apps/v3/applications/\?slug=project-slug -H “Authorization: Token API-Access-Token”` 

To get the `environment-uuid` use the `project(application)-uuid`:

  `curl https://api.divio.com/apps/v3/environments/\?application\=application-uuid -H “Authorization: Token API-Access-Token”` 


Setup your workflow
-------------------
Open the **Actions** tab, you may choose from the existing continuous deployment workflows, but for now **skip to set up a workflow yourself** and a `main.yml` file will be created for you. You can rename it anything you want. 

Commit your yaml file with the following configuration.

    # This workflow will install Divio CLI and deploy into Divio on a push event
    
    name: Divio Continuous Deployment
    
    # push event to master branch triggers the workflow
    on:
    push:
      branches: [ master ]
    
    # jobs run sequentially or in parallel to execute the workflow
    jobs:
    
    # This workflow contains a single job called "deploy"
    deploy:
      # Setting environment variables of the secret keys for easy access
      env:
        DIVIO_API_TOKEN: ${{ secrets.DIVIO_API_TOKEN }}
        TEST_ENVIRONMENT_ID: ${{ secrets.TEST_ENVIRONMENT_ID }}  
        LIVE_ENVIRONMENT_ID: ${{ secrets.LIVE_ENVIRONMENT_ID }}
      # The type of runner that this job will run on
      runs-on: ubuntu-latest
      # Steps represent a sequence of tasks that will be executed as part of the job
      steps:
      # Checks-out the repository under $GITHUB_WORKSPACE, so this job can access it
      - uses: actions/checkout@v2
          
      - name: Deploy to Divio Cloud using the API
       run: |
         # To deploy to TEST
         curl -X POST --data "environment=TEST_ENVIRONMENT_ID" --header "Authorization: Token DIVIO_API_TOKEN" https://api.divio.com/apps/v3/deployments/
         # To deploy to LIVE
         curl -X POST --data "environment=LIVE_ENVIRONMENT_ID" --header "Authorization: Token DIVIO_API_TOKEN" https://api.divio.com/apps/v3/deployments/


On the terminal: 
* Create and git initialize a directory 
* Clone your github repository
* Develop your applications and 
* `git push` to deploy to Divio.
