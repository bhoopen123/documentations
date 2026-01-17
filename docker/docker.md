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
- Significant RAM/Disk overhead
- VM Image creation takes time (in hours)
- VM provisioning too slow (in minutes)
- Boot time too slow (minutes)
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

