
# Lab 2: Docker Web Apps, running on IBM Containers

> **Difficulty**: Intermediate

> **Time**: 35 minutes

> **Tasks**:
>- [Prerequisites](#prerequisites)
- [Task 1: Pull your public images](#task-1-pull-your-public-images)
- [Task 2: Push your images to Bluemix](#task-2-push-your-images-to-bluemix)
- [Task 3: Verify security vulnerabilities](#task-3-verify-security-vulnerabilities)
- [Task 4: Run your web app](#task-4-run-your-web-app)

## What is IBM Containers?

**In this lab**, you will learn how to push images to your hosted private registry on Bluemix, evaluate security vulnerabilities for your pushed images, and run an application with two linked container images.  

Use [IBM Containers](http://www.ibm.com/cloud-computing/bluemix/solutions/open-architecture/) to run Docker containers in a hosted cloud environment on IBM Bluemix™. IBM Containers helps you build and deploy containers where you can package your applications and services. Each container is based on an image format, includes a set of standard operations, and is an execution environment in itself.

Containers are virtual software objects that include all the elements that an application needs to run. Each container includes just the app and its dependencies, running as an isolated process on the host operating system. Therefore, it has the benefits of resource isolation and allocation, but is more portable and efficient. Containers help you build high-quality apps, fast.

IBM Bluemix™ provides the IBM Containers infrastructure as a feature that is available in selected regions. Containers enable you to build your app in any language, with any programming tools. On Bluemix, you start developing with containers by using a trusted container image. With your organization’s private registry, you can automate your build pipeline and share artifacts with collaborators through public or private registries, while quickly integrating your container-based applications with over 150 Bluemix services. Containers simplify system administration by providing standardized environments for development and production teams. They help remove the complexity of managing different operating system platforms and underlying infrastructure. Containers help you deploy and run any app on any infrastructure, quickly and reliably.

For those needing an introduction on Docker, please consult https://docs.docker.com.

## Prerequisites

Prior to running this lab, you must have a Bluemix account and setup the IBM Containers command line locally.  Instructions are available in [prereqs](https://github.com/osowski/ibm-containers-codemotion/blob/master/0-prereqs.md).

If you are on a trial account, you will want to ensure that you have removed all non-essential containers, as these will impact your quota whether they are running or not.  These can be removed through the Bluemix UI or IBM Containers CLI.

## Task 1: Pull your public images

In this lab, you will work with two public images, Let's Chat and MongoDB.  First, you will need to pull them down locally before you can tag and push them to your private Bluemix registry.

1. Pull the MongoDB image from DockerHub

        $ docker pull mongo
        Using default tag: latest
        latest: Pulling from library/mongo
        68e42ff590bd: Pull complete
        b4c4e8b590a7: Pull complete
        f037c6d892c5: Pull complete
        ...
        a08422dd6a11: Pull complete
        99e2f2cde1c3: Pull complete
        202e2c1fe066: Pull complete
        Digest: sha256:223d59692269be18696be5c4f48e3d4117c7f11e175fe760f6b575387abc1bba
        Status: Downloaded newer image for mongo:latest

2. Pull the Let's Chat image from DockerHub

        $ docker pull sdelements/lets-chat
        Using default tag: latest
        latest: Pulling from sdelements/lets-chat
        7a42f1433a16: Already exists
        3d88cbf54477: Already exists
        ed1f86248ba8: Already exists
        ...
        5086886076ea: Already exists
        ca11de166bed: Already exists
        2409eb7b9e8c: Already exists
        Digest: sha256:98d1637b93a1fcc493bb00bb122602036b784e3cde25e8b3cae29abd15275206
        Status: Image is up to date for sdelements/lets-chat:latest

3. You can verify these images are correct and compatible by running the applications locally.

        $ docker run -d --name lc-mongo mongo
        6ef19c325f6fda8f5c0277337dd797d4e31113daa7da92fbe85fe70557bfcb49

        $ docker run -d --name lets-chat --link lc-mongo:mongo -p 8080:8080 sdelements/lets-chat
        4180a983e329947196e317563037bfd0da093ab89add16911de90534c69a7822

4. Access the application through your browser.  Depending on your local configuration, you may be able to use localhost as the hostname, but the most common configuration will be using `docker-machine` and will need to acquire the IP address as below.

         $ docker-machine ip default
         192.168.99.100

  In your browser, access http://192.168.99.100:8080.  

5. You can now optionally stop and remove your local running containers.

         $ docker stop lets-chat lc-mongo
         lets-chat
         lc-mongo

         $ docker rm lets-chat lc-mongo
         lets-chat
         lc-mongo

  Congratulations, you've pulled and run your first Docker-based web app.  Now you will prepare the images to run them on the IBM Containers service in the cloud.

## Task 2: Push your images to Bluemix

Now that you have pulled and run your images locally, it is time to tag them for use in IBM Containers on Bluemix.  To do so, you will need to tag them with the repository name you created when you setup your Bluemix account for IBM Container usage.

1. Log in to Bluemix and the IBM Container service

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

2. First tag your MongoDB image.  Remember to use your namespace from the first command below to replace `[NAMESPACE]` in the tag and push commands below.

         $ docker images
         REPOSITORY                                                    TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
         mongo                                                         latest              202e2c1fe066        7 days ago          261.6 MB
         sdelements/lets-chat                                          latest              2409eb7b9e8c        4 weeks ago         241.5 MB
         $ cf ic namespace get
         ibm_containers_demo_eu
         $ docker tag -f mongo registry.eu-gb.bluemix.net/[NAMESPACE]/mongo
         $ docker images
         REPOSITORY                                                    TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
         mongo                                                         latest              202e2c1fe066        7 days ago          261.6 MB
         registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo       latest              202e2c1fe066        7 days ago          261.6 MB
         sdelements/lets-chat                                          latest              2409eb7b9e8c        4 weeks ago         241.5 MB

  Note that the `IMAGE ID` column did not change for the Mongo image.  Since we are not modifying the image, but rather simply giving it another name, the `IMAGE ID` stays the same and allows us to reuse the existing container image as-is.

2. Next, tag your Let's Chat image.  Remember to use your namespace from the first command below to replace `[NAMESPACE]` in the tag and push commands below.

        $ docker images
         REPOSITORY                                                    TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
         mongo                                                         latest              202e2c1fe066        7 days ago          261.6 MB
         registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo       latest              202e2c1fe066        7 days ago          261.6 MB
         sdelements/lets-chat                                          latest              2409eb7b9e8c        4 weeks ago         241.5 MB
         $ cf ic namespace get
         ibm_containers_demo_eu
         $ docker tag -f sdelements/lets-chat registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat
         $ docker images
         REPOSITORY                                                    TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
         mongo                                                         latest              202e2c1fe066        7 days ago          261.6 MB
         registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo       latest              202e2c1fe066        7 days ago          261.6 MB
         registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat   latest              2409eb7b9e8c        4 weeks ago         241.5 MB
         sdelements/lets-chat                                          latest              2409eb7b9e8c        4 weeks ago         241.5 MB

3. Due to a current networking issue, you will need to wrap your base Let's Chat image with a simple Dockerfile to ensure network connectivity.
  1. To do so, create a new directory called `wrapper`
          mkdir wrapper
  2. Switch to that directory and run the following command to create a Dockerfile
           cd wrapper
          echo "FROM sdelements/lets-chat:latest" > Dockerfile
          echo "CMD (sleep 60; npm start)" >> Dockerfile
  3. This will create a new Dockerfile that we can build a temporary image from.
            docker build -t registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat .
  4. You will now use this image below to push to Bluemix instead of the base `lets-chat` image

4. Now that your images are tagged in the correct format, you can push them to your private registry on Bluemix.  This allows the IBM Container service to run your container images on the cloud.

         $ docker push registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo
         The push refers to a repository [registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo] (len: 1)
         Sending image list
         Pushing repository registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo (1 tags)
         Image 68e42ff590bd already pushed, skipping
         Image b4c4e8b590a7 already pushed, skipping
         f037c6d892c5: Image successfully pushed
         1a64ad3ccff1: Image successfully pushed
         7f85ac94fbfc: Image successfully pushed
         ...
         a08422dd6a11: Image successfully pushed
         99e2f2cde1c3: Image successfully pushed
         202e2c1fe066: Image successfully pushed
         Pushing tag for rev [202e2c1fe066] on {https://registry.eu-gb.bluemix.net/v1/repositories/ibm_containers_demo_eu/mongo/tags/latest}

         $ docker push registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat
         The push refers to a repository [registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat] (len: 1)
         Sending image list
         Pushing repository registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat (1 tags)
         Image adb3157c92fa already pushed, skipping
         Image ed1f86248ba8 already pushed, skipping
         Image 60643f301b72 already pushed, skipping
         ...
         Image 48b1e23d7a1a already pushed, skipping
         Image ca11de166bed already pushed, skipping
         Image 2409eb7b9e8c already pushed, skipping
         Pushing tag for rev [2409eb7b9e8c] on {https://registry.eu-gb.bluemix.net/v1/repositories/ibm_containers_demo_eu/lets-chat/tags/latest}

  Now your images are up in the cloud, in your hosted registry, and ready to run on Bluemix!  But first, take a moment to understand what is inside the images you just pushed!

## Task 3: Verify security vulnerabilities

One of the fundamental aspects of Docker containers is reuse and the ability to base your containers on top of other containers.  Think of it as inheritance for infrastructure!  But with that comes some heavy responsibility to understand what code you are running on top of and what code you are bringing into your infrastructure through a `docker pull`.  

To solve this issue, IBM Containers provides **Vulnerability Advisor**, a pre-integrated security scanning tool that will alert you of vulnerable images and can even be configured to prevent deployment of those images.  For now, you will look over the vulnerability assessment of the images you just pushed.

1. Go to the [Bluemix Dashboard](https://console.eu-gb.bluemix.net/?direct=classic/#/resources) and click on **CATALOG**.

2. Hover over the purple icon for **Mongo**.  This is the Mongo image that you pulled from the public DockerHub registry and pushed into your private registry.

  You will see a pop-up with the vulnerability assessment shown inline.  This is a red/yellow/green scale.  Your Mongo image should be a green status of **Safe to Deploy**.  

3. Click on the **Mongo** image and you are taken to the container deployment page.  You won't deploy your container from here, but you can see the vulnerability assessment in full detail.  

  On the right side of the screen, you can see your image's *Vulnerability Assessment* as well as your quota information.  The icon should read *Safe to Deploy* based on your Mongo image upload.

4. Click on **View the vulnerability report for this image**. This will bring you to the assessment details page with two tabs: **Vulnerable Packages** and **Policy Violations**.  

  The *Vulnerable Packages* tab shows you the number of packages scanned, the number of vulnerable packages present in your image, and the number of relevant security notices attached to any of those vulnerable packages.  Your image should now around 107 packages scanned with 0 vulnerable packages and 0 security notices.

  The *Policy Violations* tab shows you how the image compares against your organization's security policies.  This will show the number of rules the image was validated against and any possible policy violations.  Your Mongo image should show around 27 policy rules with 2 associated policy violations (being Password Age and Password Length).  

  As an additional homework assignment, you can create your own Dockerfile for MongoDB that builds on top of this image but fixes those security issues.  For now, you can click back in your browser to go to the image details page.

5. Click on **Manage your org's policies**.  Here you are presented with two boxes - **Deployment Settings for Containers** and **Image Deployment Impact**.  

  The *Deployment Settings for Containers* allows users with the appropriate level of authority to control which images can be deployed based on the vulnerability status.  You can see the multiple options that allows users to *Warn* or *Block* image deployment.

  The *Image Deployment Impact* shows a summary view of the state of all images in your registry.  Images can have statuses of *Deployment Blocked*, *Deploy with Caution*, and *Safe to Deploy*.  This gives you a quick look into which images are troublesome and which images are secure across your entire registry.

6. Return to the [Bluemix Catalog](https://console.eu-gb.bluemix.net/catalog/) and review the vulnerability assessment for the Let's Chat image.  You can do this by clicking on the purple **lets-chat** icon and viewing the same vulnerability information on the right hand side of the page.

You have reviewed your pushed images, which were sourced from a public repository, and can now safely deploy them on your hosted Bluemix account.  This is a key step in making sure you are running the code which you expect to be running and you are not opening your organization up to security issues, at the expense of agility.  You still want to stay secure, even when moving at light-speed!

## Task 4: Run your web app

Now that you've pushed your images to Bluemix and reviewed the contents of those images through the IBM Containers Vulnerability Advisor, you can run your images the same way you did locally but without any worry of keeping your laptop on all day, every day!  The commands here you'll run aren't that much different than what you did locally.

1. First, look and see what images are in your Bluemix hosted registry

         $ cf ic images
         REPOSITORY                                                            TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
         registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat           latest              3aeb3c224c6b        5 minutes ago       241.5 MB
         registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo               latest              ae293c6896a1        6 minutes ago       0 B
         registry.eu-gb.bluemix.net/ibm-node-strong-pm                         latest              ef21e9d1656c        13 days ago         528.7 MB
         registry.eu-gb.bluemix.net/ibmliberty                                 latest              2209a9732f35        13 days ago         492.8 MB
         registry.eu-gb.bluemix.net/ibmnode                                    latest              8f962f6afc9a        13 days ago         429 MB
         registry.eu-gb.bluemix.net/ibm-mobilefirst-starter                    latest              5996bb6e51a1        13 days ago         770.4 MB

2. Now run your Mongo container just like you did locally, except this time use `cf ic` instead of `docker` to point to Bluemix.         
         $ cf ic run --name lc-mongo -p 27017 -m 512 registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo
         71eb28dc-4d95-4a6d-bcaa-93f2382e48b5
         $ cf ic ps
         CONTAINER ID        IMAGE                                                            COMMAND             CREATED             STATUS                   PORTS               NAMES
         7ebf51a3-35a        registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo:latest   ""                  45 seconds ago      Running 27 seconds ago   27017/tcp           lc-mongo

3. Next run your Let's Chat container just like you did locally, again using `cf ic` instead of `docker` to point to Bluemix.

         $ cf ic run --name lets-chat --link lc-mongo:mongo -p 8080 -m 256 registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat
         a5dc5e0d-8eae-44a2-9f8d-548112bec250
         $ cf ic ps
         CONTAINER ID        IMAGE                                                                COMMAND             CREATED             STATUS                   PORTS               NAMES
         d368a598-69d        registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat:latest   ""                  10 seconds ago      Building 7 seconds ago   8080/tcp            lets-chat
         7ebf51a3-35a        registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo:latest       ""                  2 minutes ago       Running a minute ago     27017/tcp           lc-mongo

4. Finally, you need to expose your Let's Chat container to the public internet, so you and your team can start chatting!  You'll run the `ip list` command to see which IPs are available and then bind one to your running container.

         $ cf ic ip list
         Number of allocated public IP addresses:  2

         IpAddress        ContainerId   
         134.XXX.YYY.ZZ0       
         134.XXX.YYY.ZZ1      
         $ cf ic ip bind 134.XXX.YYY.ZZ0 lets-chat
         OK
         The IP address was bound successfully
         $ cf ic ps
         CONTAINER ID        IMAGE                                                                COMMAND             CREATED              STATUS                  PORTS                          NAMES
         d368a598-69d        registry.eu-gb.bluemix.net/ibm_containers_demo_eu/lets-chat:latest   ""                  About a minute ago   Running a minute ago    134.XXX.YYY.ZZ0:8080->8080/tcp   lets-chat
         7ebf51a3-35a        registry.eu-gb.bluemix.net/ibm_containers_demo_eu/mongo:latest       ""                  3 minutes ago        Running 3 minutes ago   27017/tcp                      lc-mongo

5. Check out your running app in your browser, at the IP you just bound.  Remember to use port `8080`!


## Conclusion

Congratulations, you have successfully completed this IBM Containers lab!.  You've just deployed your first Docker-based web app on a hosted container service!  In this lab, you learned how to tag and push local images to Bluemix, inspect pushed images for security vulnerabilities, and run hosted multi-container applications on IBM Containers.

Now you can take the hands-off approach to all your future application deployments!

## Cleanup

If you plan to do another lab, you need to clean up your container instances.  This can be done through the UI and the **DELETE** button on each container, or you can do this through the CLI with the `cf ic rm -f [CONTAINER_NAME]` command.

## Related information

Related resources and additional tutorials are available via [additional-resources](https://github.com/osowski/ibm-containers-codemotion/blob/master/99-additional-resources.md).
