# sqlapp
About this application :

This is an ASP .NET Core app uses runtime .NET 6.0 . This application retreives a product table from a Datbase hosted in Azure
Overall this lap

First we create a build pipeline and publish the content to ArtifactStaingDirectory.
Then using the Dockerfile residing in the repository , a Docker image is built. 
Once the docker image is bult we push the image to Azure Container Registy creating a repository. 
