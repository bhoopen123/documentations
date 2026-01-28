## Introduction
Before Virtualization, what all things a developer/ops team needs to check during deployment

### Possible reasons why the new deployment is failed, 
- Correct OS version on Server
- Correct .NET runtime version
- All application files (resources, etc.) and application settings
- Enough RAM / CPU
- All steps are performed in given order during deployment
- There could be dll depemdency conflicts amoung the application we have deployed and other apps already present on the server
- Required permission are missing

Such deployment is called as Bare Metal Deployment, 
following image shows how Bare Metal Deployment is and issues related to it, 

![Bare Metal Deployment](/docker/imgs/BareMetalDeployment.png)

### Virtualization

![Virtualization](/docker/imgs/Virtualization.png)

#### Benefits
Because application is deplyed on a single VM,
- OR/Runtime match
- Clean isolated environment for each deployed application
- Better resource allocation
- Built-in permissions
- Predictable paths
- Easier to scale
- Faster provisioning (in minutes)
- Simple rollback (as previous VM image can be spin-up)

#### Issues
- Significant RAM/Disk overhead since each VM has its own OS
- VM Image creation takes time (in hours)
- VM provisioning too slow (in minutes)
- Boot time too slow (minutes) since OS takes time to boot
- Limited number of VMs can be created on a server
- Wasted of resources (as each VM is having its own OS)
- Multiple OS instances requires muitiple instances to patch
- Hard to version control VM images (due to size, etc.)

### Containers: next level virtualization

![Container](/docker/imgs/Container.png)

#### Benefits
- Minimal resource overhead: Container shares 'OS Kernel' and only require 'User Space' components which reduce RAM and disk usages compare to full VMs.
- Near-instant image creation: takes seconds to create a new container since it does't copy the entire OS image.
- Fast provisioning (seconds): since it doesn't need to boot an OS.
- Environment consistency
- High density deployments: more containers can be created on a server.
- Efficient resource usage: since it directly interact with OS Kernal.
- Container images are built-in versioning: easier to track and control than VM Images.

**Then why do we need VMs if containers are so efficient ?**
- Instead of choosing, we can combine VM and container to get best of the both worlds.

![Getting the best of both](/docker/imgs/GettingBestOfBoth.png)

This hybrid approach is used by most of the cloud providers. It gives isolation of VMs between different customers or environments, while letting you get all the efficency and speed benefits of containers within each VM.

### Docker Desktop Architecture (Windows)

Docker Desktop is a developer friendly application which runs all the necessary components to run Decker.

![Docker Desktop Architecture](/docker/imgs/DockerDesktopArchitecture.png)

### WSL 2
At the very foundation, we have windows Subsystem for Linux version 2 (WSL 2). This is microsoft compatibility layer that lets you run a Linux Kernel directly on Windows. 

### WSL 2 Utility VM
WSL2 creates and manages the Utility VM, which is a lightweight virtual machine used to provide a seamless Linux environment for your containers.

### Linux Kernel under WSL 2 Utility VM
WSL 2 runs a Linux kernel inside this VM. which manages the hardware and provides essential services.

### docker-desktop (distro)
Within this Utility VM, there is a special Linux distribution called docker-desktop (distro).
Which is manages by Docker Desktop and provides the user space environment necessary for running the Docker Engine and your containers.

### Docker daemon
Its under Docker-desktop (distro), its also known as `dockerd`. This is the core process of Docker which manages all your containers, images, networks and volumes.
It listens for commands and executes them.

### Containers
These are the containers run under docker-desktop where your application run. These containers share the Linux Kernal but have their known file systems and processes.

### Docker API
To control and command. The Docker Daemon exposes RESTFul API, which acts as the interface that provides the set of commands and protocols that let you tell the daemon what to do. 

### Docker CLI
One way to interact with Docker API is through the Docker Command Line Interface (CLI).
When you type commands line docker run or docker build in your terminal, you are using the CLI, which then translates these commands into API calls that the Docker daemon will recieve and understand.

### Docker GUI
This is a user friendly graphical user interface provided by Docker Desktop to interact with the Docker Engine components. Its communicates the Docker API similar was as Docker CLI, sending commands and giving visual feedback.

### Docker Engine
This term refers to the core components that make Docker work. So, it is composed of the Docker daemon, the Docker API and Docker CLI. 

In essence, Docker Desktop orchestrates all these components providing a seamless and efficient was to run Docker containers on your developer box.

## Docker Desktop GUI
- Starting Docker Desktop doesn't mean that the containers will start, the containers need to be started manually.


## Docker Images vs Containers

### Docker Images
- Docker Image is a blueprint or template that contains everything needed to run your application. Also, each step, when performed to create a Docker image, is known as a layer, with each layer representing a specific instruction to create your containerized application.

![Docker Image](/docker/imgs/DockerImage.png)

- The whole point of building a Docker image is to share it in a place where other devices can assess it. This place is known as a container registery and could you think similar as a repository for Docker images, similar to how GitHub stores your code.
- Any device that has access to the container registry and that is running the docker Engine can now download the docker image.

- [Docker Hub](https://hub.docker.com/) is the biggest repository of publicly available Docker images in the internet.

### Docker Containers
- A docker container is a running instance of a Docker Image. It has its isolated environment with its own file system, networking, and processes all based based on the instruction on the Image.
- Inside a container, the application runs in isolation with its own resources and its own view of the operating system but sharing the same kernal as the Host.

![DockerContainer](/docker/imgs/DockerContainer.png)

## Few docker commands
- `docker version`
- `docker pull <imageName>`: download a docker image from DockerHub.
- `docker images`: view docker images on console
- `docker run <imageName>`: run a docker image in a container locally. If local image not found then download it first then run the image in a container.
- `Ctrl+C` to stop the running image from command prompt.
- `docker run -d --name <ContainerName> <ImageName>`
    - `-d` : run as detatched (in background)
    - `--name <ContainerName>` : Name of the Container you want to assign (else it will assign some random name).
- `docker run -d --rm --name <ContainerName> <ImageName>`
    - `--rm` : remove the container as soon as it is stopped.
- `docker run -d --rm -p <hostPort>:<ContainerPort> --name <ContainerName> <ImageName>` 
    - `-p <hostPort>:<ContainerPort>`: port mapping
- `docker run -d -p <hostPort>:<ContainerPort> -v <VolumeName>:<ContainerPathToBeStoredInDockerVolume> --name <ContainerName> <ImageName>`
    - `-v <VolumeName>:<ContainerPathToBeStoredInDockerVolume>` : docker volume 
- `docker ps` : to show the runing containers.
- `docker ps --all` : to show running and stopped containers.
- `docker stop <ContainerName>`: stop the running container.
- `docker rm <ContainerName>`: remove a stopped container.
- `docker rm <ContainerName> --force`: remove a running container.
- `docker volume list`: view docker volumes

## Docker Tags

![Docker Tags](/docker/imgs/DockerTags.png)

- To pull/download a specific `Tag` image from Docker Hub.
```
docker pull <imageName>:<tag version/name>
```

Example, 
```
docker pull nginx:1.26.3
```

**Note:** If we already have same docker image for different tag, docker pull will only download the different component and skip the components which are already present and not changed with resprect to the image components we are downloading.

As we know that one image can have more than one Tags, if we download the same image with different tag, then docker will not download the image again, but it will show both the images downloaded locally when running command `docker images`.

## Port Mapping
The Docker containers are completely isolated by default. We will tell docker to map a port on our host machine, lets say port 8080 to port 80 inside the container. Port mapping is a bridge between host network and container.

Now the brower can send the request to localhost:8080 which configured port mapping will be forwarded by docker to port 80 inside container.
Nginx (inside container) processes the request successful and send response through our mapped ports ending in our browser.

![Port Mapping](/docker/imgs/PortMapping.png)

`
docker run -d --rm -p <hostPort>:<containerPort> --name <containerName> <imageName>
`

Example,

`
docker run -d --rm -p 8080:80 --name nginx-test nginx
`

## Opening up a bash session on a container
`
docker exec -it <containerName> /bin/bash
`

Example,

`
docker exec -it nginx-test /bin/bash
`

`-it` : means interactive session.

- type `exit` to exit from docker console.

## Docker volumes
Lets say, there is a file system in a container. And our application has choosen to store all of the user generated files in the "path to data".
There is something crucial to understand about Docker containers. They are designed to be ephemeral. When a container is removed or deleted, anything stored inside it disappears completely. So when we create a new container for the same application, all the data is gone.
It starts only with the original file structure from its corresponding Docker image.
How to prevent losing data when renewing a container?
`Docker Volumne` can help in this.

A great feature of Docker is the ability to reserve space for persistent storage. This is a dedicated space on the host machine that Docker manages. Within this persistance space, we can create a volume.
Think of it as a special folder that Docker manages for us.

When we start a docker container, and can tell Docker to add to data directory to this Volume. This creates a direct link whatever our application writes to that path actually gets stored in the Volume.
Even if the comtainer gets deleted, our data remains safely stored in the volume.

And the best part is, when we creata a new container we can connect it to the same volume. Now the new container has access to all the data which was stored by the previous container(s).

![Docker Volume](/docker/imgs/DockerVolume.png)

### Runinhg a docker with volume
`-v` for volume: 
<VolumeName>:<LocationOfTheContainerToLinkToOurVolume>

Example,

`
docker run -d [--rm] -p 8080:80 -v nginx-data:/usr/share/nginx/html --name nginx-test nginx
`

- View docker volumes

`
docker volume list
`

## Docker image creation options

1. Dockerfile
2. .NET SDK

### Building container image using Dockerfiles
As we know that a Docker image is a blueprint or template that contains everything needed to run the application. One way to define this template via a Dockerfile.
This is a simple text where you specify all the instructions that are needed to prepare the Docker image.

For example, 
1. Select OS/.NET image
2. Copy app files
3. Install dependencies
4. Configure environment
5. Run app

You place this file next to your application code and then execute the Docker build command. That command talks to a docker component called buildkit, which drives the build process.
`buildkit` creates a docker image by reading one by one each of the instructions on the docker file and generating layers or adding metadata to the image depending on the type of the instruction.
At the end of the build process, the new docker image is ready to be used in local or can be published to a container registry in the cloud.

**Example,** 

Create a .NET application

Open CMD -> publish the application

`dotnet publish .\hello-docker.csproj -o published`

- This will create application binaries in the `published` folder, Note: the projecti will be published in the `Release` mode. 
- This command will also create an exe file which is specific to windows OS. So, we don't need to bundle the exe file during publish.

`dotnet publish .\hello-docker.csproj -o published /p:UseAppHost=false`
- This will create application binaries in the `published` folder without exe file. And it is common practice (excluding exe file) while creating a docker image.

**Writing a Dockerfile**
- Install Docker extension in VSCode.
- Create a dockerfile with name `dockerfile` in the solution directory.
- There are docker images for different .NET sdks/runtimes [Docker hub repositories](https://learn.microsoft.com/en-us/dotnet/core/docker/container-images#docker-hub-repositories). Select `aspnet` as we are building .NET web application docker image.
- Now these aspnet code image is generally going to be based on Linux and not on windows. Because Linux images are much more smaller, more efficient, faster, and are broadly supported by most of the container orchestrator systems like Kubernetes. And since .NET is cross platform, you can easily use any image that is based on any Linux distribution.

View [full tag listing](https://github.com/dotnet/dotnet-docker/blob/main/README.aspnet.md#full-tag-listing) to know which OS is used in which docker image.

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0

WORKDIR /app

COPY published/ ./

ENTRYPOINT [ "dotnet", "hello-docker.dll" ]
```

- In order to build Docker Image, run following commands
- In VS code -> Ctrl + J (this will open terminal window)

`
docker build -t <DockerImageName> <locationOfBuildContext>
`

- locationOfBuildContext : are set of files that are going to be transfered into the docker deamon.

Example, 

`
docker build -t hello-docker .
`

**Note:** here .(dot) specifies the current directory.
This will create a docker image with the given image.

- run docker image, 

`
docker run --rm -p 8080:8080 my-docker-image
`


