# Save a Container Image to a Central Private Container Registry (Azure Container Registry - a.k.a. "ACR")

The purpose of this section is to help you understand how to clone/copy images via the PULL (from public image repositories), TAG (update versioning and/or destination registry) and PUSH (to registry) flow with the docker command line tool (aka docker client).

- Once you have an existing Container Image, the next step is to tag and push your Custom Container Image to Private Registry, in our case ACR.
    - If you want to try this on your own, the high-level flow will look like the following:
        - Create an Azure Container Registry (ACR)
            - ```az acr create --resource-group <your_resource_group> --name <your_registry_name> --sku Basic```
        - Re-tag your custom built image and namespace it to your private registry (hint: ```<your_registry_name>.azurecr.io/<image_name>```)
            - ```docker tag myapp:latest <your_registry_name>.azurecr.io/myapp```
        - Login to your Private Azure Container Registry (i.e. make Docker CLI aware of your private registry)
            - ```az acr login --name <your_container_registry>```
        - Push your custom built image to your private Azure Container Registry
            - ```docker push <your_registry_name>.azurecr.io/myapp```
            - Docker knows to push your image to your registry based on the image name, which includes the full domain/FQDN of your registry -> ```<your_registry_name>.azurecr.io```
                - without a domain/FQDN/URL as the first part of your docker image, Docker CLI assumes you want to push to Docker Hub by default

![pull/tag/push](images/docker_push_to_registry.png)

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