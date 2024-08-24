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

# About azure-pipeline.yaml file

The YAML file you provided is an Azure Pipeline configuration for building and pushing a Docker image to Azure Container Registry (ACR). Here’s a breakdown of what it does:

### Key Sections:

1. **Trigger:**
   - The pipeline is triggered by changes to the `main` branch of the repository.

2. **Resources:**
   - It uses the `self` repository (the same repository where this pipeline is defined).

3. **Variables:**
   - `dockerRegistryServiceConnection`: This is the service connection ID to connect to Azure Container Registry (ACR).
   - `imageRepository`: The name of the Docker image repository (`sqlappdocker`).
   - `containerRegistry`: The URL of the Azure Container Registry (`azcrazdevopseastus.azurecr.io`).
   - `dockerfilePath`: The path to the Dockerfile located in the `sqlapp` directory.
   - `tag`: The build ID used as a tag for the Docker image.
   - `vmImageName`: Specifies the VM image to use for the build agent, which is `ubuntu-latest`.

4. **Stages:**
   - **Build Stage:**
     - This stage is responsible for building and pushing the Docker image.
     - **Jobs:**
       - **Job Name: `Build`**
         - Uses an Ubuntu agent (`ubuntu-latest`).
         - **Steps:**
           1. **Install .NET SDK:**
              - Uses the `UseDotNet@2` task to install the .NET SDK version 6.x.
           2. **Build the .NET Application:**
              - Uses `DotNetCoreCLI@2` to build all `.csproj` projects in the repository.
           3. **Publish the .NET Application:**
              - Publishes the build output to the `$(Build.ArtifactStagingDirectory)` directory.
           4. **Build and Push Docker Image:**
              - Uses the `Docker@2` task to build the Docker image from the Dockerfile located in `$(Build.ArtifactStagingDirectory)/sqlapp`.
              - Pushes the built image to the Azure Container Registry (`azcrazdevopseastus.azurecr.io`).
              - Tags the image with the build ID and `latest`.

### Summary:
- The pipeline builds a .NET application, publishes the output, and then builds a Docker image using the published application.
- The Docker image is tagged and pushed to Azure Container Registry.
- The pipeline is triggered by changes to the `main` branch.

This YAML file automates the process of building and deploying a Docker image for a .NET application to Azure Container Registry.
