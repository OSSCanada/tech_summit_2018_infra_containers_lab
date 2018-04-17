# How to Pull and Use a Docker Container Image

The purpose of this section is to help you understand how to clone/copy images via docker commands using:
    - PULL (from public image repositories)
    - TAG (update versioning and/or destination registry)
    - PUSH (to registry) with the Docker command line tool (aka Docker client).

## Pulling an Image from Docker Hub (Public Container Registry)

We will pull in a publicly accessible image (https://hub.docker.com/r/kevingbb/bobble/) from Docker Hub to our remote Linux VM (jumpbox).  This is a container encapsulates a pre-built Node.js app that also levarages Azure Cognitive Services behind the scenes.

In general we pull in publicly accessible images to use as a base to create our own custom images.  For this lab - we will not be customizing or building our own container.  Instead we will "re-tag" this image and push it into our own private repository for secure and authenticated access.

To pull the image to our local environment run the following command:

```
docker pull kevingbb/khbobble
```

This command tells the Docker Command Line to connect to Docker Hub
- the ```kevingbb``` portion is in the form of a user/organization name, and the Docker command line/engine assumes to lookup this account via Docker Hub by default
- if you replace ```kevingbb``` with a name that looks like a FQDN/Domain name e.g. ```myrepo.azurecr.io```, docker now uses this as the endpoint it should reach out to pull the image from
    - We will leverage this when pushing container images to a private repository

## Creating an Azure Container Registry

We will now create an Azure Container Registry to push our re-tagged container image to.  This serves as a privately hosted location to share and deploy container images from.

Instructions to create Azure Container Registry (ACR) - Click [here](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal).

## Re-Tagging a container image

Tagging/Re-tagging a container image gives us the ability to redeploy/rename and version our container images.

We will need the full container registry URL for this exercise, as it will be used to tell the Docker Command Line Client to target our registry.

To retag our image run the following command:
```
docker tag kevingbb/khbobble <your_registry_name>.azurecr.io/khbobble
```

This will essentially create an alias to the container image that ```kevingbb/khbobble``` points to.  If you run a ```docker images``` command you will see two container images with different repository names, however they both will point to the same ```IMAGE ID```.

## Login into Azure Container Registry

We will need to authenticate to our container registry in order to be able to push/pull to it.  You can do this through one of two methods:

### Method #1 - Azure CLI

The Azure CLI has a help sub command for ```acr``` that will help us pull in the necessary credentials and automatically make Docker aware of our repository.  For this we will need our repository name.

Assuming you're already logged into the Azure CLI you may run the following command: 

```
az acr login --name <your_registry_name>
```

Again, this will automatically add the required credentials for Docker to connect to your registry - and transparently uses Method#2 under the hood.

### Method #2 - Docker Native

Docker gives you ability to login to any Docker Hub V.2 complian registry.  You will need the FQDN, registry name and password for your Azure Container Registry.

```
docker login <your_registry_name>.azurecr.io -u <your_registry_name> -p <your_registry_password>
```

This will create the necessary credential file for Docker to authenticate to your registry going forward, you will not need to reauthenticate unless your username/password expire.

## Pushing to a Azure Container Registry

We have setup the required steps to allow us to push our contianer image to our registry.  To do so we can run the following command:

```
docker push <your_registry_name>.azurecr.io/khbobble
```

Note that we need to name our image with the FQDN to our private registry - this is how Docker is able to logically connect your registry with the correct image to push.

## Summary

We now have a container image in a privately hosted Container Registry on Azure, and can leverage this image to deploy to a number of container compatible services in Azure.  Our next step is to create an Azure App Service: Web App for Containers Servie to deploy our container to.

### Notes:

- By default the ```docker``` command knows to ```pull``` from docker hub registry
- In order to ```push``` to your registry you must be logged in by running ```docker login <your-private-repo-url>```
    - ```docker login``` by itself will log you in to docker hub
        - you must include a URL to authenticate against a privately hosted registry (like Azure Container Registry)
    - after logging into your registry, credentials are cached for future use
        - for Azure Container Registry your username is the same as ```<registry_name>```.azurecr.io
        - the password can be found in your Azure Container Registry Dashboard in the Azure Portal
- You can check your cached credentials in your ```.docker/config.json``` in your home folder
    - you will see the registries your're logged into and can ```push``` to
- By tagging images with ```<registry_name>/<image_name>``` docker will resolve this name to the proper ```<registry_name>``` to push/pull to
    - simple names like ```kevingbb/<image_name>``` are likely docker hub registries
    - complex names like ```<registry_name>.azurecr.io/<image_name>``` are privately hosted registries (not docker hub)