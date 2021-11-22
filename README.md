# CZ-IBM-Cloud-Guide

The purpose of this project is to deploy a simple node.js application to Kubernetes on IBM Cloud.

![Diagram](doc/overview.png)

---
## Prerequisities
1. You are going to work with your command line/terminal. On MacOS, look for Terminal. On Windows, look for Command Prompt (Command line), and if you have Linux, you will surely know :)
1. Install  [git for Windows](https://git-scm.com/download/win), [git for Linux](https://git-scm.com/download/linux), or [git for macOS](https://git-scm.com/download/mac)
1. Install [Docker](https://docs.docker.com/get-docker/) of version at least 19.03.13
1. Install [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cli-getting-started)
1. Install [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cli-getting-started) plugin for _Kubernetes Service_

    ```
    $ ibmcloud plugin install kubernetes-service
    ```
1. Install [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cli-getting-started) for _Container Registry_
    ```
    $ ibmcloud plugin install container-registry
    ```
---
## Tutorial
1. Clone the repository

    Open command line and navigate to your preffered folder/directory. 

    Examle:
    ```
    $  cd Desktop/MyProjects
    ```
    Clone this repository:

    ```
    $  git clone https://github.com/IBM/CZ-IBM-Cloud-Guide.git
    ```
    > If you are facing any troubles, look at the official documentation for [cloning a repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository)


1. Create IBM Cloud account [here](https://cloud.ibm.com/registration). To create an account follow the tutorial in the IBM Cloud Guide in chapter 4.
1. Login to your IBM Cloud account [here](https://cloud.ibm.com/login)
1. Create `Kubernetes Service` in IBM Cloud [here](https://cloud.ibm.com/kubernetes/catalog/create)
    1. As `Pricing plan` choose `Free`
    1. Optionally change `Cluster name` and `Resource group`
    1. Click `Create`
1. Create `namespace` in `Container Registry` [here](https://cloud.ibm.com/registry/namespaces)
    1. Optionally change `Resource group`
    1. Paste `Name` 
    1. Click `Create`
1. Login to IBM Cloud
     ```
    $ ibmcloud login -sso 
    ```
    Output:
    >Paste `y` to get one time password in browser and then copy and paste the one time password. Otherwise open the URL in browser manually.
    ```
    API endpoint: https://cloud.ibm.com
    Region: eu-de

    Get a one-time code from https://identity-2.uk-south.iam.cloud.ibm.com/identity/passcode to proceed.
    Open the URL in the default browser? [Y/n] > 
    ``` 
    
1. Login to the Container Registry
    ```
    $ ibmcloud cr login
    ```
    Output:
    
    ```
    Logging in to 'registry.eu-de.bluemix.net'...
    Logged in to 'registry.eu-de.bluemix.net'.
    Logging in to 'de.icr.io'...
    Logged in to 'de.icr.io'.

    OK
    ``` 
1. Get your Kubernetes cluster context

    ```
    $ ibmcloud ks cluster config --cluster <your_cluster_id>
    ```
    >In order to be able to deploy an application to your cluster, you need to store the context of the cluster locally, for which you need to obtain a Cluster ID. Therefore, go to the Kubernetes Cluster in the user interface in the browser via the upper left menu, select [Resource list](https://cloud.ibm.com/resources), then click on Clusters and select your cluster. Here you can see and copy the Cluster ID.

    Output:
    
    ```
    Logging in to 'registry.eu-de.bluemix.net'...
    Logged in to 'registry.eu-de.bluemix.net'.
    Logging in to 'de.icr.io'...
    Logged in to 'de.icr.io'.

    OK
    ``` 

1. Create Docker Image
    
    Navigate to the root of this project. To make sure you are in the root paste the following command and see if you can see your repository:

    For MacOs/Linux
     ```
    $ ls 
    ```

    For Windows:
      ```
    $ dir
    ```

    Output:
    ```
    Dockerfile      
    README.md       
    app             
    deployment.yml
    ```
    To create a docker image, run the following command:
     ```
    $ docker build -t <provider>/<my_namespace>/<my_repo>:<my_tag> .
    ```
    >The `<provider>` is a name of the container registry provider. Depending on your provider choose the correct name. In case of the container registry in IBM Cloud it depends on the location of your registry. To obtain the correct value, go to [images](https://cloud.ibm.com/registry/images) in IBM Cloud, choose your location and click **create**. The `<my_namespace>` is the namespace you have created in Container Registry. `<my_repo>` will be created automatically. `<my_tag>` is the name of your image and you can optionally add version.
    

    The tag is now visible by running this command:
     ```
    $ docker image ls
    ```

1. Push Docker Image to your Container Registry

    ```
    $ docker push <provider>/<my_namespace>/<my_repo>:<my_tag>
    ```
    > The created Docker Image needs to be pushed to the Container Registry in order for Kubernetes Service to retrieve this Docker Image. Your image will now be displayed in the IBM Cloud Container Registry under [images](https://cloud.ibm.com/registry/images).
    
1. Update the image tag (name) in `deployment.yml` file instead of `*`
    ```
    spec:
      containers:
        - name: nodejs
          image: *
          imagePullPolicy: Always
          ports:
            - containerPort: 3000
    ```

1. Deploy the application into Kubernetes cluster
    >The file `deployment.yml` in the root of the project contains a script, which processes the deployment automatically. The objective is to apply this deployment template.
    ```
    $ kubectl apply -f deployment.yml
    ```
    
1. Navigate to your `Kubernetes cluster` in IBM Cloud, click on `Worker nodes` and copy the `Public IP`. 

    Paste the IP adress to your browser and add nodePort `30080`.
    >The port can be changed in `deployment.yml` file.

    Example:
    ```
    http://159.122.177.240:30080
    ```
    Congratulations! Now you are able to see the application in browser!



