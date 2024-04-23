# UDO - M1 - Priprema za ispit
&nbsp;
# Lab 04 - Advanced container concepts
--------------------------------------------------------------------------------------------------------------------------------

#### Introduction
In this lab we will learn network, volumes, bind mounts, and docker/podman compose.
Container networking allows containers to communicate with each other and with external systems, enabling seamless connectivity within distributed applications.
Volumes and bind mounts provide mechanisms for persisting data in containers, with volumes offering independent storage and bind mounts linking host file system directories into containers.
Docker Compose and Podman Compose are tools for defining and managing multi-container applications through simple configuration files, streamlining the deployment and orchestration of complex container environments. They both follow the Compose Specification.

--------------------------------------------------------------------------------------------------------------------------------

##### **Task 1**: Log in into workstation VM in RH academy.
- **Guide**: 
  - Go to https://rha.ole.redhat.com/.
  - Log in
  - Click Launch in the bottom right corner of the course name
  - Click on the Lab Environment tab
  - Click the Start button above the list of VMs
  - When the workstation VM starts, click Open Console

##### **Task 2**: By using **podman network** command list all podman networks. It should only show default network called **podman** with the **bridge** driver.
- **Explanation**: Display all networks managed by Podman on the system.
- **Command**: `podman network ls`
- **Expected Output**: Should show only the default network named 'podman' using the bridge driver.

##### **Task 3**: Inspect the network configuration of the default podman network. What is the value of dns_enabled setting?
- **Explanation**: Examine the configuration details of the default network to check DNS settings.
- **Command**: `podman network inspect podman`
- **Question**: What is the value of `dns_enabled` setting?
- *NOTE*: The value of the `dns_enabled` setting on the default podman network seems to be `false`

##### **Task 4**: Create a custom network which will have dns enabled. Name it labnet.
- **Explanation**: Set up a new network configuration to be used by containers that require DNS.
- **Command**: `podman network create --subnet 10.88.5.0/24 --gateway 10.88.5.1 --dns-enable true labnet`
- *NOTE*: The `--dns-enable` command seems unsupported, just omit it and it will be enabled by default. Use `podman network create --help` for more info.

##### **Task 5**: Create a mysql database container by using official mysql image(https://hub.docker.com/_/mysql). It should randomize the root password, create a database called wordpress and a user called student with password DB15secure!. It should be started in the background and connected to labnet network. Name the container mysql.
- **Explanation**: Deploy a MySQL container with a randomized root password, a new database named 'wordpress', and a user for the database.
- **Command**: 
  ```bash
  podman run -d --name mysql --network labnet \
    -e MYSQL_RANDOM_ROOT_PASSWORD=yes \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=student \
    -e MYSQL_PASSWORD=DB15secure! \
    mysql:latest
  ```
- *NOTE*: This can also be done by specifying the base image and environment variables in a Dockerfile, and then building an image using that Dockerfile (example follows).
- *Dockerfile contents*:
  ```dockerfile
  # Use the official MySQL image as the base
  FROM mysql:latest

  # Set environment variables
  ENV MYSQL_RANDOM_ROOT_PASSWORD=yes \
    MYSQL_DATABASE=wordpress \
    MYSQL_USER=student \
    MYSQL_PASSWORD=DB15secure!

  # When the container starts, these environment variables will be set automatically
  ```
- *Build command (run from dockerfile directory)*: `podman build -t custom-mysql .`
- *Run command*: `podman run -d --name mysql --network labnet custom-mysql`

##### **Task 6**: Create a wordpress container by using official wordpress image(https://hub.docker.com/_/wordpress). It should be started in the background, connected to the labnet network and it should publish port 80 from the container to port 8080 on the host. Configure environment variables to connect to database container created in the previous step. Wen referencing the database host use mysql(name of the previous container) as the host.
- **Explanation**: Start a WordPress container that connects to the MySQL database created in the previous task.
- **Command**: 
  ```bash
  podman run -d --name wordpress --network labnet -p 8080:80 \
    -e WORDPRESS_DB_HOST=mysql \
    -e WORDPRESS_DB_USER=student \
    -e WORDPRESS_DB_PASSWORD=DB15secure! \
    -e WORDPRESS_DB_NAME=wordpress \
    wordpress:latest
  ```
- *NOTE*: This can also be done by specifying the base image and environment variables in a Dockerfile, and then building an image using that Dockerfile (see example above).

##### **Task 7**: If you repeated previous two steps by using default network, would it work?
- **Explanation**: Determine if using the default network instead of a custom network would affect the functionality of the containers.
- **Question**: Would it work using the default network?
- *NOTE*: It should work using the default network.

##### **Task 8**: Create a default web page in the wordpress application.
- **Explanation**: Modify or add a default page to the WordPress site.
- **Steps**: Log into the WordPress admin panel and create or edit a page as needed.

##### **Task 9**: In case you needed to delete containers to replace them with updated images or for any other reason, all the files in the read write layer of the image would be lost. To persist the data bind mounts and volumes are used. Delete the old mysql container, and create a new one which will bind mount the container directory /var/lib/mysql to host directory /home/student/mysql. Wait for the container to start and examine the host directory.
- **Explanation**: To avoid data loss when containers are deleted, use bind mounts to persist data on the host.
- **Commands**: 
  ```bash
  podman rm -f mysql  # Remove the old container
  podman run -d --name mysql --network labnet \
    -v /home/student/mysql:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=mysecret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=student \
    -e MYSQL_PASSWORD=DB15secure! \
    mysql:latest
  ```
- **Check**: Inspect `/home/student/mysql` to verify that MySQL data is persisting on the host.
- *NOTE*: If you're having issues with the local directory you're binding to, navigate to the directory manually using `dir`, `cd` and `pwd` commands. Then just say `-v .:/var/lib/mysql`
- *NOTE*: You may run into issues with the container not having the correct permissions to access the directory. Try `sudo setenforce 0` as a bruteforce solution. To undo this use `sudo setenforce 1`.

##### **>>> ADDITONAL RELEVANT NOTES**:	
  - *NOTE*: To diagnose issues with container initialization use `podman logs <container-name>`
  - *NOTE*: To list all existing containers use `podman ps -a`
  - *NOTE*: To delete a container use `podman rm -f <container-name>`
  - *NOTE*: To start and stop a container without deleting it, use `podman start <container-name>` and `podman stop <container-name>`

##### **Task 10**: Recreate the previously created container, this time using volume instead of a bind mout.
- **Explanation**: Use Podman volumes for a more manageable and Docker-like way of handling persistent data.
- **Commands**: 
  ```bash
  podman volume create mysql_data
  podman run -d --name mysql --network labnet \
    -v mysql_data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=mysecret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=student \
    -e MYSQL_PASSWORD=DB15secure! \
    mysql:latest
  ```

##### **Task 11**: Install podman-compose package.
- **Explanation**: Podman-compose is a tool to define and run multi-container applications with Podman, similar to docker-compose.
- **Command**: Depending on the system, typically `sudo dnf install podman-compose` or `sudo yum install podman-compose` or `sudo apt-get install podman-compose`.
- **NOTE**: These are different package managers (**for Cent0S 9 use dnf**):
  - RedHat-based systems, such as CentOS, Fedora, or RHEL (Red Hat Enterprise Linux) use yum (older) or dnf (newer).
  - Debian-based systems, such as Ubuntu use apt-get.

##### **Task 12**: Take a look at samples of compose found in https://github.com/containers/podman-compose/tree/main/examples and https://github.com/docker/awesome-compose.
- **Explanation**: Podman-compose is a tool to define and run multi-container applications with Podman, similar to docker-compose.
- **Resources**:
  - [Podman Compose Examples](https://github.com/containers/podman-compose/tree/main/examples)
  - [Awesome Compose](https://github.com/docker/awesome-compose)

##### **Task 13**: Create a compose file which you can use to deploy wordpress and mysql.
- **Explanation**: Write a Podman Compose file that defines the WordPress and MySQL services, ensuring they are properly linked and configured.
- **Compose File Example**:
  ```yaml
  version: '3'
  services:
    wordpress:
      image: wordpress:latest
      ports:
        - "8080:80"
      environment:
        WORDPRESS_DB_HOST: mysql
        WORDPRESS_DB_USER: student
       WORDPRESS_DB_PASSWORD: DB15secure!
        WORDPRESS_DB_NAME: wordpress
      networks:
        - labnet
    mysql:
      image: mysql:latest
      environment:
        MYSQL_ROOT_PASSWORD: mysecret
        MYSQL_DATABASE: wordpress
        MYSQL_USER: student
        MYSQL_PASSWORD: DB15secure!
      networks:
        - labnet
  networks:
    labnet:
      external: true
  ```

##### **Task 14**: Deploy them by using the compose file you created.
- **Explanation**: Use the created compose file to deploy the WordPress and MySQL containers.
- **Command**: `podman-compose up -d`

##### **>>> ADDITONAL RELEVANT NOTES**: 
  - *BRIEF PODMAN COMPOSE GUIDE*:
    - Navigate to the directory where you want to create the compose file (and potentially associated dockerfiles) using `dir`, `cd` and `pwd` commands.
    - Use `touch podman-compose.yaml` to create the compose file from terminal
    - Edit the compose file accordingly using a text editor
    - To start the containers defined in the compose file use `podman-compose up -d`
    - To stop the containers defined in the compose file use `podman-compose down`
  - *NOTE*: When writing compose yaml files, use **spaces** for indentation, not tabs
  - *NOTE*: If you have a compose file that is named differently `use podman-compose -f <filename> up -d` and `podman-compose -f <filename> down`
  - *NOTE*: You can instruct the compose file to build container images from dockerfiles, as well as starting them.
    - To simply start a container from an image use:
	  ```yaml
        services:
          wordpress:
            image: wordpress:latest
      ```
	- To build a container image from a dockerfile, and then start it use:
	  ```yaml
        services:
          wordpress:
            build:
              context: .
              dockerfile: Dockerfile
      ```

--------------------------------------------------------------------------------------------------------------------------------
### Additonal reading:
- https://github.com/containers/podman/blob/main/docs/tutorials/basic_networking.md
- https://www.redhat.com/sysadmin/container-networking-podman
- https://docs.podman.io/en/latest/markdown/podman-network-create.1.html
- https://github.com/containers/podman-compose
- https://github.com/docker/awesome-compose
- https://github.com/compose-spec/compose-spec/blob/master/spec.md
- https://docs.docker.com/compose/compose-application-model/
- https://docs.podman.io/en/v4.4/volume.html
- https://www.redhat.com/sysadmin/behind-scenes-podman
- https://sysdig.com/learn-cloud-native/
&nbsp;