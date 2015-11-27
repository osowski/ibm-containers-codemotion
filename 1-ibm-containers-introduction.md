
# Lab 1: Introduction to IBM Containers

> **Difficulty**: Easy

> **Time**: 30 minutes

> **Tasks**:
>- [Prerequisites](#prerequisites)
- [Task 1: Verify your environment](#task-1-verify-your-environment)
- [Task 2: Deploy your first container on Bluemix](#task-2-deploy-your-first-container-on-bluemix)
- [Task 3: Deploy a container through the command line](#task-3-deploy-a-container-through-the-command-line)

## What is IBM Containers?

**In this lab**, you will learn how to get started using IBM Containers, a hosted offering for managing Docker containers on IBM Bluemix™.

Use [IBM Containers](http://www.ibm.com/cloud-computing/bluemix/solutions/open-architecture/) to run Docker containers in a hosted cloud environment on IBM Bluemix™. IBM Containers helps you build and deploy containers where you can package your applications and services. Each container is based on an image format, includes a set of standard operations, and is an execution environment in itself.

Containers are virtual software objects that include all the elements that an application needs to run. Each container includes just the app and its dependencies, running as an isolated process on the host operating system. Therefore, it has the benefits of resource isolation and allocation, but is more portable and efficient. Containers help you build high-quality apps, fast.

IBM Bluemix™ provides the IBM Containers infrastructure as a feature that is available in selected regions. Containers enable you to build your app in any language, with any programming tools. On Bluemix, you start developing with containers by using a trusted container image. With your organization’s private registry, you can automate your build pipeline and share artifacts with collaborators through public or private registries, while quickly integrating your container-based applications with over 150 Bluemix services. Containers simplify system administration by providing standardized environments for development and production teams. They help remove the complexity of managing different operating system platforms and underlying infrastructure. Containers help you deploy and run any app on any infrastructure, quickly and reliably.

For those needing an introduction on Docker, please consult https://docs.docker.com.  

## Prerequisites

Prior to running this lab, you must have a Bluemix account and setup the IBM Containers command line locally.  Instructions are available in [prereqs](https://github.com/osowski/ibm-containers-codemotion/blob/master/0-prereqs.md).

## Task 1: Verify your environment

Docker Engine should be installed and running in your machine. To verify that Docker is running, do the following:

1. Verify that you are running a recent Docker version via the docker version command.  You should see something similar to the following:

        $ docker version
        Client:
         Version:      1.8.3
         API version:  1.20
         Go version:   go1.4.2
         Git commit:   f4bf5c7
         Built:        Mon Oct 12 18:01:15 UTC 2015
         OS/Arch:      darwin/amd64

        Server:
         Version:      1.8.3
         API version:  1.20
         Go version:   go1.4.2
         Git commit:   f4bf5c7
         Built:        Mon Oct 12 18:01:15 UTC 2015
         OS/Arch:      linux/amd64

2. Run a simple container locally, using the `hello-world` image

        $ docker run hello-world

        Hello from Docker.
        This message shows that your installation appears to be working correctly.

        To generate this message, Docker took the following steps:
        1. The Docker client contacted the Docker daemon.
        2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
        4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

        To try something more ambitious, you can run an Ubuntu container with:
        $ docker run -it ubuntu bash

        Share images, automate workflows, and more with a free Docker Hub account:
        https://hub.docker.com

        For more examples and ideas, visit:
        https://docs.docker.com/userguide/

3. Configure the Cloud Foundry CLI to work with the nearest IBM Bluemix region.  This ensures you will be working with the London region of Bluemix and not waiting for all your traffic to travel across the Atlantic Ocean.

        $ cf api https://api.eu-gb.bluemix.net
        Setting api endpoint to https://api.eu-gb.bluemix.net...
        OK

4. Log in to Bluemix through the Cloud Foundry CLI

        $ cf login
        API endpoint: https://api.eu-gb.bluemix.net

        Email> osowski@us.ibm.com

        Password>
        Authenticating...
        OK

        Select an org (or press enter to skip):
        1. osowski@us.ibm.com
        2. IBM_Containers_Demo_Org

        Org> 2
        Targeted org IBM_Containers_Demo_Org

        Targeted space IBM_Containers_Demo_Org_EU



        API endpoint:   https://api.eu-gb.bluemix.net (API version: 2.40.0)   
        User:           osowski@us.ibm.com   
        Org:            IBM_Containers_Demo_Org   
        Space:          IBM_Containers_Demo_Org_EU

5. Log in to the IBM Container service on Bluemix

        $ cf ic login
        Client certificates are being retrieved from IBM Containers...
        Client certificates are being stored in /Users/osowski/.ice/certs/containers-api.eu-gb.bluemix.net...
        OK
        Client certificates were retrieved.

        Checking local Docker configuration...
        OK

        Authenticating with registry at host name registry.eu-gb.bluemix.net
        OK
        Your container was authenticated with the IBM Containers registry.
        Your private Bluemix repository is URL: registry.eu-gb.bluemix.net/ibm_containers_demo_eu

        You can choose from two ways to use the Docker CLI with IBM Containers:

        Option 1: This option allows you to use "cf ic" for managing containers on IBM Containers while still using the Docker CLI directly to manage your local Docker host.
        	Use this Cloud Foundry IBM Containers plug-in without affecting the local Docker environment:

        	Example Usage:
        	cf ic ps
        	cf ic images

        Option 2: Use the Docker CLI directly. In this shell, override the local Docker environment to connect to IBM Containers by setting these variables. Copy and paste the following commands:
        	Note: Only Docker commands followed by (Docker) are supported with this option.

 	        export DOCKER_HOST=tcp://containers-api.eu-gb.bluemix.net:8443
         	export DOCKER_CERT_PATH=/Users/osowski/.ice/certs/containers-api.eu-gb.bluemix.net
         	export DOCKER_TLS_VERIFY=1

        	Example Usage:
        	docker ps
        	docker images

Congratulations!  You are now configured and logged in to use IBM Containers.  The following labs will use a mix of the UI and CLI, based on your successful setup here.

## Task 2: Deploy your first container on Bluemix

1. Go to the Bluemix dashboard for the same organization you logged into above.  This is available via [Bluemix Dashboard](https://console.eu-gb.bluemix.net/?direct=classic/#/resources).  

2. Click on **Start Containers** and you will be taken to your organization's Catalog, containing all the public and private images stored for your organization.

  You have not pushed any images to your private registry, so you should only see the following images:
  - `ibm-mobilefirst-starter` - Starter image for IBM MobileFirst Platform Foundation
  - `ibm-node-strong-pm` - Starter image for IBM Strongloop Process Manager
  - `ibmliberty` - IBM-Supported WebSphere Liberty Profile runtime container image
  - `ibmnode` - IBM-Supported Node.js runtime container image  

3. Select the `ibmliberty` image from the list of available images.

4. You are taken to the **Create Container** page.

  This is a user-interface driven method to deploying container instances on IBM Containers.  The same capability is available through the CLI, but you will be using the UI to deploy your first container on IBM Bluemix.

5. Choose the space in your organization where you would like to host the container. At this point, you should probably only have the `dev` space.  

  Select `dev`.

  In Bluemix, spaces provide a mechanism to collect related applications, services, containers, and the users that can collaborate on the resources. You can have one or more spaces within an organization.

6. Provide a name for the container.  

  Enter `libertydemo1`.  This is the same as the `--name` parameter when using the Docker CLI.

7. Choose the size of the container.

  Select **Pico (64 MB Memory, 4 GB Storage)**.  IBM Containers allows you to select right-sized hardware resources for your container instances to allow for cost-effective runtimes.  *Note*: The size of a container cannot be changed once it has been created.

8. Associate a publicly-routed IP address to the newly created container.

  Select the option **Request and bind a public IP**.  

  In Bluemix, you are provided with a default private network for all your running container instances automatically.  You can optionally select container instances to be assigned Public IP addresses and expose them to the public internet.  This is useful for web frontends and load balancing containers, while your database and similarly functioning containers can remain walled off from public internet traffic.

9. When deploying through the Bluemix user interface, container images have their exposed ports automatically detected.  You should see the following ports automatically detected when viewing the `ibmliberty` image: **22/tcp, 9080/tcp, 9443/tcp**

10. Click **CREATE**.  

  Your container instance will now be deployed to Bluemix.  You will be redirected to the dashboard overview for this new container instance.  

  In future tasks and labs you will interact with elements under the *Advanced Options* and *Vulnerability Assessment*, but feel free to explore before clicking **CREATE**.  These additional options allow you to work with volumes stored on Bluemix, bind your containers to over 150 Bluemix services, and inject your own SSH key into each container you deploy.

11. From the dashboard overview, you should see the Public IP address assigned to your running WebSphere Liberty container.

  Find your Public IP address and enter it into a browser, like *http://[Public_IP_Address]:9080*.  You should be presented with the **Welcome to Liberty** landing page.

  Alternatively, you can click on the **9080** port that is linked from the dashboard page to open the container instance via the exposed port.  You should see the same **Welcome to Liberty** landing page.

12. Back in the dashboard overview page in Bluemix, you are presented with all the controls necessary to manage your container instance.  You are also presented with pre-integrated Logging & Monitoring for all your container instances.  

  Click on **Monitoring and Logs** on the left hand menu and you will be taken to deeper view into the provided monitoring & logging stack.  This is a multi-tenant ELK (Elasticsearch, Logstash, Kibana) stack that your containers will automatically route their standard logging to.  You can configure your containers to log additional information directly to this endpoint as well.  

  This is a key piece in enabling container-based infrastructures, as it becomes quickly unmanageable to interact with all the singular instances at the lowest level.  This is just one of a few capabilities that IBM Containers provides to reduce the amount of time users take to get value out of Docker containers and hosted infrastructure.  You'll touch on a few more in subsequent labs.

13. Once you have verified your container was successfully deployed, you can delete your running container instance via the **DELETE** button on the container dashboard.  

  As you are most likely on a Trial account, removing containers after each lab will make sure you are making efficient use of your Trial quota.

## Task 3: Deploy a container through the command line

1. Make sure you are still logged in to the IBM Containers command line from Task 1.  You can verify this via `cf ic images`.  This will show a list of all the images in your organization's repository, which should be the four public IBM images so far.

         	$ cf ic images
         	REPOSITORY                                                            TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
         	registry.eu-gb.bluemix.net/ibm-node-strong-pm                         latest              ef21e9d1656c        13 days ago         528.7 MB
         	registry.eu-gb.bluemix.net/ibmliberty                                 latest              2209a9732f35        13 days ago         492.8 MB
         	registry.eu-gb.bluemix.net/ibmnode                                    latest              8f962f6afc9a        13 days ago         429 MB
         	registry.eu-gb.bluemix.net/ibm-mobilefirst-starter                    latest              5996bb6e51a1        13 days ago         770.4 MB

2.  You will be using the IBM Containers plug-in to the Cloud Foundry CLI.  This is a wrapper CLI to the Docker CLI, with some key additions for managing container groups, namespaces, and network options.  For specific command help, you can run `cf ic help` for more information on any given command.

3.  In this task, to create a container you will use one of the default IBM certified images that are available by default in your organization's private registry.  Create a container with the following parameters, deploying the IBM Liberty container image again:

  `cf ic run --name container-lab-1 -p 9080 -m 64 registry.eu-gb.bluemix.net/ibmliberty`

  where:
   - `-p` specifies the exposed port of the image, similar to the Ports field in **Task 2, Step 9** above.  

     When you specify a port, you are making the application available to the Bluemix Load Balancer and other containers attempting to access that port.  If a port is specified in the Dockerfile for the image that you are using, include that port.  

      For the IBM certified Liberty Server image, enter port 9080 via `-p 9080`.  
      For the IBM certified Node.js, enter port 8000 via `p 8000`.  
      If you plan on logging in to the container via SSH, add port 22 via `-p 22`.

   - `--name` specifies the name you can use to refer to the container
   - `-m` specifies the memory limit (and associated storage capacity), similar to **Task 2, Step 7** above
   - `registry.eu-gb.bluemix.net/ibmliberty` is the repository name for the image as displayed by the **cf ic images** command

4. To check that the container has been deployed successfully, run the `cf ic ps -a` command to show all the container artifacts that have been created or deployed in your organization.

         	$ cf ic ps -a
         	CONTAINER ID        IMAGE                                                            COMMAND             CREATED            STATUS                  PORTS                                                                                     NAMES
         	a055ba5e-77b        registry.eu-gb.bluemix.net/ibmliberty:latest                     ""                  5 minutes ago      Running 4 minutes ago   134.XXX.YYY.ZZZ:9080->9080/tcp                                                             container-lab-1

5. Now you can assign a public IP to the container.  The `cf ic run` command will proactively attempt to request and bind a public IP address for your container instance if there is room left in your IP Quota.  By default, you have 2 Public IP addresses in your quota.  

  If your `cf ic ps -a` command returns your Liberty container with ports in the form of `134.XXX.YYY.ZZZ:9080->9080/tcp`, then your container already has an assigned Public IP address and you can skip the next step.

6. (Optional) You will first list all requested Public IP addresses and then bind an available IP address to your running container instance.

         	$ cf ic ip list -a
         	Number of allocated public IP addresses:  2

         	IpAddress         ContainerId   
         	134.XXX.YYY.174   df6ba7fd-436e-4d57-a69b-4228e19cadc1   
         	134.XXX.YYY.175       

  Choose an IP address that is not bound to a container.  Note that in Bluemix, you are allocated 2 Public IPs free of charge. If you do not have any public IP addresses available to complete the lab, use the `cf ic ip release` command to release one. In a real environment, contact your Bluemix organization administrator to provision additional public IP addresses for your organization.

  Once you have identified the desired IP address (selecting *134.XXX.YYY.175* from above), you run the `cf ic ip bind` command to associate a running container instance to a public IP address.  The `cf ic ip bind` command takes either the container name or the container ID as the second argument to support scriptable interactions.

           	$ cf ic ip bind 134.XXX.YYY.175 container-lab-1
            Successfully bound IP

7. Validate access to the container through the browser.  Run `cf ic ps -a` once again and copy the public IP address associated with your container.  Enter this value into your browser via *http://[Public_IP_Address]:9080* and you should see the **Welcome to Liberty** landing page.  

8. Since both the UI and the CLI are working with the same backend, you can view your newly CLI-created container instance in the UI as well.  This can be done by returning to the [Bluemix Dashboard](https://console.eu-gb.bluemix.net/?direct=classic/#/resources).  

9. Click on **CONTAINERS** in the left-hand menu.  Your dashboard is now filtered to show your running container instances.  Clicking on the square with the `container-lab-1` title will take you to the container overview dashboard.  From here you have access to the same Monitoring & Logging covered in **Task 2, Step 12**, as well as the controls available to Stop, Pause, Restart, & Delete containers.

## Conclusion

Congratulations, you have successfully completed this IBM Containers lab!  You learned how to work with IBM Containers, and deploy Docker images onto Bluemix through the UI & CLI.

## Cleanup

If you plan to do another lab, you need to clean up your container instances.  This can be done through the UI and the **DELETE** button on each container, or you can do this through the CLI with the `cf ic rm -f [CONTAINER_NAME]` command.

## Related information

Related resources and additional tutorials are available via [additional-resources](https://github.com/osowski/ibm-containers-codemotion/blob/master/99-additional-resources.md).
