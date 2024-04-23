# UDO - M1 - Priprema za ispit
&nbsp;
# Lab 03 - Building container images
--------------------------------------------------------------------------------------------------------------------------------

#### Introduction
In this lab we will learn how to build container images and different Dockerfile instructions. Also we will learn how to configure the container to automatically start with the VM and automatically pull the new image when it is available.
A Dockerfile is a text file that contains a set of instructions used to build a Docker image. These instructions define the environment and configuration needed for running a specific application or service within a container. Dockerfiles follow a straightforward syntax and allow developers to automate the creation of container images, ensuring consistency across different environments.
As you continue working with containers, there will be a need to automate the start of containers with the host operating system. When using docker this is achieved with restart policies, and when using podman this is achieved with systemd services or quadlets.

--------------------------------------------------------------------------------------------------------------------------------

##### **Task 1**: Based on the source code available at https://github.com/docker/getting-started-app containerise the application. There is a guide available at https://docs.docker.com/get-started/02_our_app/. For solving of this task use podman instead of docker.
- **Explanation**: Clone the repository from GitHub and create a Dockerfile (or Containerfile) to build a container image using Podman. Adapt Docker commands to Podman as needed.
- **Steps**:
  - Clone the repository: `git clone https://github.com/docker/getting-started-app`
  - Navigate to the app directory: `cd getting-started-app`
  - Use the Dockerfile provided and modify commands from `docker` to `podman` as required.
- **Command**: `podman build -t getting-started-app .`

##### **Task 2**: Build this image again, but this time tag it with 0.0.1.
- **Explanation**: Rebuild the image with a specific tag to manage versions of the container image.
- **Command**: `podman build -t getting-started-app:0.0.1 .`

##### **Task 3**: Inspect the image contents by using **podman image inspect**.
- **Explanation**: Use Podman to inspect the detailed configuration and layers of your container image.
- **Command**: `podman image inspect getting-started-app:0.0.1`

##### **Task 4**: Review all available Dockerfile instructions at https://docs.docker.com/reference/dockerfile/.
- **Explanation**: Familiarize yourself with all the Dockerfile instructions to understand how they direct the building of Docker images.
- **Resource**: [Dockerfile Reference](https://docs.docker.com/reference/dockerfile/)

##### **Task 5**: Change the application so a different message appears when you have no to do items to complete. There is a guide available at https://docs.docker.com/get-started/03_updating_app/.
- **Explanation**: Edit the application's source code to change the displayed message, demonstrating an update to the containerized application.
- **Steps**:
  - Modify the relevant files in the application source to change the message.
  - Save changes and prepare to rebuild the image.

##### **Task 6**: Build this image again, but this time tag it with 0.0.2.
- **Explanation**: Build the updated application into a new image to differentiate it from the previous version.
- **Command**: `podman build -t getting-started-app:0.0.2 .`

##### **Task 7**: See the difference between these two images by using **podman diff** command.
- **Explanation**: Compare the changes between two container images to see what has been altered in the filesystem.
- **Command**: `podman image diff getting-started-app:0.0.1 getting-started-app:0.0.2`

##### **Task 8**: Create another repository on Docker Hub and push the images there. There is a guide available at https://docs.docker.com/get-started/04_sharing_app/.
- **Explanation**: Push your local images to a new repository on Docker Hub to share or back them up online.
- **Steps**:
  - Create a new repository on Docker Hub.
  - Login to Docker Hub using Podman: `podman login`
  - Push the images: `podman push getting-started-app:0.0.1` and `podman push getting-started-app:0.0.2`

##### **Task 9**: Create another Containerfile which will use ubi8 image as a base image. Update all installed packages and install httpd. Configure the image in a way that httpd is started as container is created. Name the image myweb and tag it 0.0.1. Note that the image is in a private repository to which you can login with **podman login**.
- **Explanation**: Create a new Containerfile that starts from the Universal Base Image (UBI) 8, updates packages, installs httpd, and configures it to run on container startup.
- **Containerfile**:
  ```dockerfile
  FROM ubi8
  RUN yum update -y && yum install -y httpd
  CMD ["httpd", "-D", "FOREGROUND"]
  ```
- **Build Command:** `podman build -t myweb:0.0.1 .`

##### **Task 10**: Create a quadlet file to configure the system so a container named web from image myweb:0.0.1 is started with the system. There is a guide available at https://www.redhat.com/sysadmin/quadlet-podman.
- **Explanation**: Use a quadlet file to configure your system to start a container named 'web' from the 'myweb:0.0.1' image automatically at system startup.
- **Resource**: [Guide to using quadlet with Podman](https://www.redhat.com/sysadmin/quadlet-podman)
- *NOTE*: I wasn't able to really figure this out.

##### **Task 11**: Clone git repository available at https://github.com/jstanesic/example-go-app. Build a container image by using Dockerfile Dockerfile1 and tag it example:Dockerfile1. Build another image by using Dockerfile Dockerfile2 and tag it example:Dockerfile2. Check sizes of the images. Discover what benefits multi-stage builds has in use cases when application needs to be compiled.
- **Explanation**: This demonstrates how to manage multiple container builds from a single repository, highlighting the benefits of multi-stage builds.
- **Steps**:
  - Clone the repository: `git clone https://github.com/jstanesic/example-go-app`
  - Navigate to the repository directory.
  - Build using Dockerfile1: `podman build -f Dockerfile1 -t example:Dockerfile1 .`
  - Build using Dockerfile2: `podman build -f Dockerfile2 -t example:Dockerfile2 .`
- **Check Sizes**: `podman images | grep example`
- **Multi-stage Builds Benefit**: Multi-stage builds allow for smaller final images by separating the build environment from the runtime environment, reducing overall image size and security surface.

--------------------------------------------------------------------------------------------------------------------------------
### Additonal reading:
- https://docs.docker.com/reference/dockerfile/
- https://linuxhandbook.com/autostart-podman-containers/
- https://systemd.io/
- https://catalog.redhat.com/software/containers/rhel7/57ea8cee9c624c035f96f3af?architecture=amd64&image=65e70f46befb5000328d4d90
- https://github.com/nigelpoulton/psweb
- https://developers.redhat.com/articles/2023/05/17/build-lean-nodejs-container-images-ubi-and-podman
- https://earthly.dev/blog/docker-multistage/
- https://docs.docker.com/build/building/multi-stage/
- https://docs.bitnami.com/tutorials/optimize-docker-images-multistage-builds/
- https://earthly.dev/blog/docker-multistage/
&nbsp;