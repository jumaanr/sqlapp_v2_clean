# sqlapp_v2_docker
About this application :

This is an ASP .NET Core app that uses runtime .NET 6.0. This application retrieve a product table from a Database hosted in Azure
Overall this lap

First we create a build pipeline and publish the content to ArtifactStaingDirectory.
Then, using the Dockerfile residing in the repository, a Docker image is built. 
Once the docker image is built, we push the image to Azure Container Registry, creating a repository. 

# sqlapp_v2_debug

Created a new branch for the application, for debugging to find out why we instruct docker to get the content from Build.ArtifactStagingDirectory/sqlapp directory to get the published content.
The output shows that the actual sqlapp.dll file resides inside the sqlapp directory under Build.ArtifactStagingDirectory.
Refer to the respective pipeline file as azure-pipeline-debug.yaml

When you recreate this project, use the same source code, but the pipeline file is azure-pipeline-debug.yaml
The following code has been added.

    - pwsh: |       
       Get-ChildItem -Path $(Build.ArtifactStagingDirectory)\*.* -Recurse -Force | Out-String -Width 180
      errorActionPreference: continue
      displayName: 'List content'
      continueOnError: true

When we are running the Build pipeline, we can see where the actual publish content is saved. 
![image](https://github.com/user-attachments/assets/a1705b6d-f381-4ef3-b801-cb8b8d1127e8)

