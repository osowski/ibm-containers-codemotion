
# Lab 3: Docker Web Apps with IBM Bluemix services

> **Difficulty**: Intermediate

> **Time**: 35 minutes

> **Tasks**:
>- [Prerequisites](#prerequisites)
- [Task 1: Create a bridge application](#task-1-create-a-bridge-application)
- [Task 2: Deploy application from existing repository](#task-2-deploy-application-from-existing-repository)
- [Task 3: Review deployment automation steps](#task-3-review-deployment-automation-steps)
- [Task 4: Run your web app](#task-4-run-your-web-app)

## What is IBM Containers?

**In this lab**, you will learn how to bind Bluemix Services to Docker containers, deploy existing applications with one-click via the *Deploy to Bluemix Button*, and understand how the IBM Bluemix DevOps Pipeline can build & deploy your container images automatically.

Use [IBM Containers](http://www.ibm.com/cloud-computing/bluemix/solutions/open-architecture/) to run Docker containers in a hosted cloud environment on IBM Bluemix™. IBM Containers helps you build and deploy containers where you can package your applications and services. Each container is based on an image format, includes a set of standard operations, and is an execution environment in itself.

Containers are virtual software objects that include all the elements that an application needs to run. Each container includes just the app and its dependencies, running as an isolated process on the host operating system. Therefore, it has the benefits of resource isolation and allocation, but is more portable and efficient. Containers help you build high-quality apps, fast.

IBM Bluemix™ provides the IBM Containers infrastructure as a feature that is available in selected regions. Containers enable you to build your app in any language, with any programming tools. On Bluemix, you start developing with containers by using a trusted container image. With your organization’s private registry, you can automate your build pipeline and share artifacts with collaborators through public or private registries, while quickly integrating your container-based applications with over 150 Bluemix services. Containers simplify system administration by providing standardized environments for development and production teams. They help remove the complexity of managing different operating system platforms and underlying infrastructure. Containers help you deploy and run any app on any infrastructure, quickly and reliably.

For those needing an introduction on Docker, please consult https://docs.docker.com.

## Prerequisites

Prior to running this lab, you must have a Bluemix account and setup the IBM Containers command line locally.  Instructions are available in [prereqs](https://github.com/osowski/ibm-containers-codemotion/blob/master/0-prereqs.md).

If you are on a trial account, you will want to ensure that you have removed all non-essential containers, as these will impact your quota whether they are running or not.  These can be removed through the Bluemix UI or IBM Containers CLI.

## Task 1: Create a bridge application

IBM Containers have the advantage of running on IBM Bluemix and because of that, you can leverage any of the 150+ Bluemix services inside your running containers.  To do this, you will need to create a Bluemix application to expose your desired services to your containers.

1. Go to the [Bluemix Dashboard](https://console.eu-gb.bluemix.net/?direct=classic/#/resources) and click on **CREATE APP**.
2. Select any runtme and click **NEXT**.  Either NodeJS or Liberty is recommended, but any runtime is acceptable as we won't be interacting with the deployed application, just leveraging its bound services.
3. Enter a unique name for your application and click **DEPLOY**.  This must be unique across all the entire Bluemix region your are working in.  For instance, "testapp" is a less than desirable name.  Something like "[YOUR_NAME]-bridge-app" is much better.  This will become the hostname for your app once deployed on Bluemix.
4. Once your application has been deployed and you are taken to the **Getting Started** page, click on the **Overview** tab on the left.
5. Now you will need to bind a MongoDB service instance to your application.  This will then expose the credentials to your container at runtime.  Click on **NEW SERVICE OR API**.
6. Search for or scroll to **MongoDB by Compose** and click on it.
7. The credentials that you will need to enter on this page will be shared with you during the lab.  These credentials are Host, Port, Username, and Password.  Enter the provided credentials and click **CREATE**.  All other defaults on the page are acceptable.

  Alternatively, you can sign up for a *Free 30-Day Trial* at [Compose](https://compose.io) and use all of the features beyond just today.  If you are interested in cloud application development, I'd highly recommend signing up for your own Compose account, as they provide a number of offerings that are critical for efficient application development - MongoDB, PostgreSQL, Redis, etcd, and many more!
8. Once you are prompted to restage your application, click **RESTAGE** and wait a few moments for your application to be running again.
9. Since you will be using some pre-built automation to deploy your application, you will need to rename your bridge application to a more standard name.  This will only change the application name inside your space and not the hostname (which is the property that must be unique across all of the Bluemix region).

  From the **Overview** tab of your application, click on the cog icon in the upper-right corner and select **RENAME APPLICATION**.  Enter a new name of **lets-chat-bridge** and click **OK**.  

  Your application data in the UI should reflect this naming change.  Note that your bound hostname did not change at all.

Now you've created the necessary services for your container to leverage Mongo as a Service!

## Task 2: Deploy application from existing repository

Previous labs walked you through manually deploying containers on Bluemix from scratch.  Some of the great reasons for using containers is speed and that doesn't come with doing everything by hand.  This task will walk you through using the IBM Bluemix Delivery Pipeline and the one-click *Deploy to Bluemix* button to speed that process up quite a bit!

1. Go to GitHub and this [demo repository](https://github.com/osowski/lets-chat-bluemix-simple) for the labs.

2. There is a lot more detail on containers, Let's Chat, and deploying on Bluemix.  But you've already done the pre-requisites so just click on the **Deploy to Bluemix** button for an even easier deployment.

3. You are taken to a new page which will require you to login.  You may need to setup your Jazz account if this is your first time.  If so, simply click **Log in** and you will be taken through the appropriate steps to create your shortname.

4. Once you are provided with the option to deploy, select an appropriate and unique app name, along with the following from the dropdowns:
  - Region: IBM Bluemix United Kingdom
  - Organization: [YOUR_EMAIL_ADDRESS]
  - Space: dev *(unless you created another space and have been working in there so far)*

5. Click **DEPLOY** and you will be taken to another page where you can watch the live deployment of your container-based application, all from a pre-built repository without ever needing to touch the code.

  As the automation goes through forking the project into your own account, setting up a pipeline for your account, and building & deploying those images on Bluemix, you will be updated in the UI.  Once the status page returns complete after a few minutes, you can move on to the next Task.

## Task 3: Review deployment automation steps

Now that your project has been deployed to IBM Containers on Bluemix, let's review what really just happened.  Bluemix forked the GitHub project into a Jazz Hub project, IBM's hosted source code management platform.  Inside this project a build pipeline was imported from the `.bluemix/pipeline.yaml` file in the original repository.  This pipeline will automatically build the Docker image for Let's Chat, push it to your private registry in Bluemix, and then deploy a running container instance on IBM Containers.

1. To view the project, right-click on **Created project successfully** and open the link in a new tab.  This will show you the standard project view with the forked code from GitHub.

2. Right now, you are interested in the Build Pipeline that was automatically created, so click on **BUILD & DEPLOY** in the upper right.  This will take you to the Build Pipeline view of your project and show you the two stages that were created for build and deploy of your container image.

3. Click on the gear icon in the top-right of the **Build Docker Images** box and select **Configure Stage**.  This will take you to the configuration view of your *Build* step.  

4. At the top of the page, click on the **INPUT** tab.  Here you can see the pipeline will pull from the forked project automatically.  When creating projects from scratch, you can connect directly with public GitHub projects, however for ease of use, the *Deploy to Bluemix* button uses the forked repository for quicker deployments.

5. Click on the **JOBS** button at the top of the page and you can see all the necessary information for building your image dynamically whenever a code change is pushed to your repository.  You can select which region, which organization, which space, and what to name your container image.  There is a default script that is included in this step which is more than sufficient, but you can modify it as needed.

6. Scroll to the bottom and click **CANCEL**.

7. Click on the gear icon in the top-right of the **Deploy Let's Chat Docker Image** box and select **Configure Stage**.  This will take you to the configuration view of your *Deploy* step.

8. At the top of the page, click on the **INPUT** tab again.  Here you will see the inputs for your *Deploy* step.  This step can be run manually or automatically, taking the input from the previous step's output (that being your *Build* step in this case).

9. Click on the **JOBS** button at the top of the page and you can see all the necessary information for deploying your image to IBM Containers on Bluemix.  This again allows you to select your specific region, organization, space, and image to deploy.

  Multiple deployment strategies are available, but by default *red_black* is selected to allow for maximum up-time of your container-based application.  Additional deployment strategies will be available soon.

  The *Ports* of your image are available to be exposed here and you can expose all, some, or none, depending on your pipeline needs.  Due to *IBM Containers* being a managed-Docker environment, you need to expose ports if you want your container to be accessible from other containers.  Otherwise, it will run fine but not be able to receive inbound communication from any other containers.  You only need to expose ports via the *image only format* (`-p 8080`), as the service will manage the host port mapping for all containers.

  The default deployment script is more than sufficient but can be modified to suit your needs, for something like dynamic linking, external lookups, integration with CMDBs or Software Config tools, etc.  You can also pass in `docker run` command-line arguments via the *Optional deploy arguments* field for things like `--link`, `--volume`, or `--env`.

10. Once you have reviewed the **Deploy** step, click **CANCEL** at the bottom of the page.

  You have now reviewed the automated deployment of a Docker-container project on IBM Containers, running on Bluemix.  For additional homework, you can create your own [IBM Bluemix DevOps Services](https://hub.jazz.net/) project pulling from a public GitHub repository and build your own pipeline to deploy Docker containers on Bluemix.


## Task 4: Run your web app

1. Get Public IP of your deployed container.  This can be found through the Bluemix Delivery Pipeline output of the **Deploy** stage via the **View logs and history** link.  

  The specific line will look something like `Public IP address of lets-chat-a_1 is 134.XXX.YYY.ZZZ and the TEST_URL is http://134.XXX.YYY.ZZZ:8080`.

  Alternatively, you can see this from the Bluemix UI or the IBM Containers CLI via the `cf ic ps` command.

2. Go to http://[YOUR_PUBLIC_IP]:8080 in your browser and you've got your web app up & running!  No coding or command line needed!

3. Now every change that would come into your repository can kick off a new Docker image build and push that image into deployment through the dev/test/production cycle.  The delivery pipelines can be configured to automatically or manually build, deploy, and move images throughout multiple stages.  It's all up to you!

## Conclusion

Congratulations, you have successfully completed this IBM Containers lab!.  You've just automatically deployed your Docker-based web app on a hosted container service, through an automated pipeline!  In this lab, you learned how to automate Docker image build and deployment, as well as bind Bluemix services to your running IBM Containers.

Now you can take the no-code approach to all your future application deployments!

## Cleanup

If you plan to do another lab, you need to clean up your container instances.  This can be done through the UI and the **DELETE** button on each container, or you can do this through the CLI with the `cf ic rm -f [CONTAINER_NAME]` command.

## Related information

Related resources and additional tutorials are available via [additional-resources](https://github.com/osowski/ibm-containers-codemotion/blob/master/99-additional-resources.md).
