# UDO - M1 - Priprema za ispit
&nbsp;
# Lab 02 - Container Management
--------------------------------------------------------------------------------------------------------------------------------

#### Introduction
In this lab we will explore the difference between docker and podman and learn how to manage container.

--------------------------------------------------------------------------------------------------------------------------------

##### **Task 1**: Connect to the workstation VM inside RH Academy lab environment.
- **Guide**: 
  - Go to https://rha.ole.redhat.com/.
  - Log in
  - Click Launch in the bottom right corner of the course name
  - Click on the Lab Environment tab
  - Click the Start button above the list of VMs
  - When the workstation VM starts, click Open Console

##### **Task 2**: By using **podman search** find latest nginx container image from docker hub.
- **Explanation**: Searches Docker Hub for the latest versions of the nginx image.
- **Command**: `podman search nginx`

##### **Task 3**: Find the documentation explaining the use of that image.
- **Explanation**: Locates the official documentation page for the nginx image on Docker Hub.
- **Documentation**: [Nginx Docker Hub](https://hub.docker.com/_/nginx)

##### **Task 4**: List running containers on the system by using **podman ps**.
- **Explanation**: Displays all containers that are currently running on your system.
- **Command**: `podman ps`

##### **Task 5**: Pull the image you found by using podman pull.
- **Explanation**: Downloads the latest nginx image from Docker Hub to your local machine.
- **Command**: `podman pull nginx`

##### **Task 6**: Find more information about the container image by using **podman image inspect**.
- **Explanation**: Provides detailed information about the nginx image, such as its ID, creation time, and size.
- **Command**: `podman image inspect nginx`

##### **Task 7**: Create a container instance from that image in the background and map it to a host port 80 by using **podman run**. Are you encountering any issues?
- **Explanation**: Starts a new container from the nginx image in detached mode and maps port 80 of the container to port 80 on the host.
- **Command**: `podman run -d -p 80:80 nginx`
- **Note**: Check for port conflicts or other issues.

##### **Task 8**: Create an instance of that container in the background and map it to a host port 8080. Name this container web8080. Are you encountering any issues?
- **Explanation**: Runs a new instance of the nginx container in detached mode, maps port 80 of the container to port 8080 on the host, and names the container "web8080".
- **Command**: `podman run -d -p 8080:80 --name web8080 nginx`

##### **Task 9**: List running containers.
- **Explanation**: Lists all containers that are currently active on your machine.
- **Command**: `podman ps`

##### **Task 10**: Stop the first container you created by using **podman stop**.
- **Explanation**: Stops the running container identified by its container ID.
- **Command**: `podman stop <container-id>`
- **Note**: Use `podman ps` to find the container ID.

##### **Task 11**: Try creating another container instance named web8080. Are you encountering any issues? How can you resolve this issue?
- **Explanation**: Attempts to create a new container with the name "web8080", which will fail if a container with the same name already exists.
- **Resolution**: Remove the existing container with `podman rm web8080`, then retry the creation command.

##### **Task 12**: Inspect the details of web8080 container by using **podman inspect**.
- **Explanation**: Provides a detailed view of the container's configuration and state, such as network settings and mounted volumes.
- **Command**: `podman inspect web8080`

##### **Task 13**: List all the images you have downloaded by using **podman images** command.
- **Explanation**: Displays a list of all container images currently stored on your local system.
- **Command**: `podman images`

##### **Task 14**: Export the nginx image by using **podman image save** or **podman export**. What is the difference between these two commands?
- **Explanation**: 
  - `podman save` is used to save a container image to a tar file, which can be used to share or archive.
  - `podman export` is used to export the current state of a container's filesystem to a tar file.
- **Commands**:
  - `podman save -o nginx.tar nginx` (saves the image to a tar archive)
  - `podman export <container-id> -o nginx-container.tar` (exports the container's filesystem)
- **Difference**: `save` deals with images; `export` deals with the state of containers.

##### **Task 15**: Find the command which you can use while creating a container image, manually modifying and then saving that as a new image.
- **Explanation**: Starts an interactive session inside a running nginx container, allows you to make modifications, and then saves those changes as a new image.
- **Commands**:
  - `podman run -it --name custom-nginx nginx bash`
  - After modifications: `podman commit custom-nginx custom-nginx-image`

##### **Task 16**: Find and use the command you can use to change the contents of the default web page of web8080 container without terminating it.
- **Explanation**: Executes a command inside the running web8080 container to change the contents of the default index.html file.
- **Command**: `podman exec -it web8080 bash -c 'echo "Custom Content" > /usr/share/nginx/html/index.html'`

##### **Task 17**: Answer the following questions:
- **a. Do containers lose their state when they are terminated and are container tools changing that behaviour?**
  - **Answer**: Yes, unless data is stored in volumes or changes are committed to a new image.
- **b. Do you need to pull the container image before creating a container?**
  - **Answer**: Not if the image exists locally, but pulling ensures you have the latest version.
- **c. Which command can you use to import a podman image?**
  - **Answer**: `podman import`
- **d. Does podman support restart of container images?**
  - **Answer**: Yes, with `podman restart <container-id>`

--------------------------------------------------------------------------------------------------------------------------------
### Additonal reading:
- https://docs.podman.io/en/v4.4/Commands.html
&nbsp;