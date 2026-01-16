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