# Day 1

## Info - Installing docker community edition in Ubuntu
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER
su $USER
docker --version
docker info
docker images
```

## Info - Server grade Processors
<pre>
- Processors are packaged in Integrated Circuits(IC)
- ICs comes in 2 Form factors
  - SCM(Single chip module)
    - one Processor per IC
  - MCM(Multiple Chip module )
    - multiple Processors per IC
    - for example, a single IC could host 4 Processors in it
- assume, you have a server grade motherboard that supports 8 Sockets
  - if we install MCM Processor with 4 Processors per Socket
  - total 4 x 8 sockets = 32 Processors
  - assume, each Processor supports 96 physical cpu core, which means 96 x 2 = 192 virtual cores
  - total virtual cpu cores = 192 x 32 = 6144 virtual cores
</pre>  

## Info - Hypervisor Overview
<pre>
- Hypervisor is nothing but virtualization technology
- Virtualization technology allows to run multiple Operating Systems on a single machine
- many OS can run simultaneously
- this is a Hardware + Software technology
  - Processor should support virtualization
    - AMD Processor
      - the virtualization feature set is called AMD-V
    - Intel Processor
      - the virtualization feature set is called VT-X
    - we need a machine that supports either AMD-V or VT-X to host multiple VMs
- there are 2 types of Hypervisors
  - Type 1 
    - used in Servers & Workstations
    - a.k.a bare-metal hypervisor
    - examples
      - KVM
      - VMWare vSphere/vCenter
  - Type 2
    - used in laptops/desktops/workstations
    - can be installed on top of some OS( Host OS - windows, linux, mac os-x, etc., )
    - examples
      - VMWare Workstation ( supported in Linux & Windows )
      - VMWare Fusion ( supported in Mac OS-X )
      - Oracle virtualbox ( supported in Linux, Windows & Mac OS-X )
      - Parallels ( supported in Mac OS-X )
      - Microsoft Hyper-V
- the Operating systems that run within the Virtual Machine(VM) are called Guest OS
- for each VM dedicated Hardware resources must be allocated
  - CPU Cores
  - RAM
  - Storage ( HDD/SDD )
- hence, this type of virtualization is called heavy-weight virtualization
- each VM represents one fully-functional Operating System
</pre>

## Info - Hypervisor High-Level Architecture
![Hypervisor](HypervisorHighLevelArchitecture.png)

## Info - Containerization
<pre>
- is an application virtualization technology
- each container represents one application process
- containers running on the same machine/OS shares the hardware resources on the Host OS
- containers are not Operating System
- containers don't get their own dedicated hardware resources, hence it is referred as light-weight virtualization
- containers don't have their own OS kernel
- container technology is not a replacement for Virtualization or Operating System
- Linux Kernel features that enables containerization
  1. Control Groups ( CGroups ) 
    - helps in applying resource quota restricts on the container level
    - using this feature, one can restrict how much CPU, RAM or Storage one container can utilize at the max at any point of time
  2. Namespace
    - helps in isolatating one container from other containers running on the same Host OS
</pre>

## Info - Container Runtime
<pre>
- is a low-level software that helps us manage container images and containers
- interacts with OS Kernel to manage container images and containers
- is not so user-friendly, hence end-users like us don't use this software utility directly
- examples
  - runC
  - CRI-O
</pre>

## Info - Container Engine
<pre>
- is a high-level software that helps us manage container images and containers
- under the hood, it depends on Container Runtime to manage container images and containers
- is very user-friendly
- examples
  - Docker
  - Podman
</pre>

## Info - Docker High-Level Architecture
![Docker](DockerHighLevelArchitecture.png)

## Info - Container Image
<pre>
- is a blueprint of a container
- within a single container image, we can create any number of container instances called containers
- they are similar to windows10.iso image
</pre>  

## Info - Containers
<pre>
- is a running instance of a Container Image
- every container runs in a separate namespace
- each container has a 
  - unique name
  - unique id
  - unique hostname
  - unique IP Private ( Private IP )
  - dedicated network stack ( 7 OSI Layers )
  - one or more dedicated Network Cards ( software defined virtual network cards similar to virtualization )
  - its own file system
- one container represents one application
- container to container is possible
- all containers that runs in same namespace they can talk to each other by default
- containers running in separate namespace won't be able to communicate with each other
</pre>

## Lab - Checking the version of docker
```
docker --version
docker info
```

## Lab - Listing the docker images in your local docker registry
```
docker images
```

Expected output
<img width="1146" height="718" alt="image" src="https://github.com/user-attachments/assets/41a96690-5ef3-4314-b8bf-2a5372bfcce1" />

## Lab - Download ubuntu image from Docker Hub Remote Registry to Local Docker Registry
```
docker pull ubuntu:latest
docker pull mysql:latest
docker pull wordpress:latest
docker pull nginx:latest
docker pull nginx:1.18
docker pull nginx:1.19
docker images
```

## Lab - Creating a container in background mode(daemon)
Create a container and run it in background
```
docker run -dit --name ubuntu1 --hostname ubuntu1 ubuntu:latest /bin/bash
docker run -dit --name ubuntu2 --hostname ubuntu2 ubuntu:latest /bin/bash
docker run -dit --name ubuntu3 --hostname ubuntu3 ubuntu:latest /bin/bash
```
In the above command
<pre>
d - indicates you wish to run the container in the background(daemon) mode
it - indicates interactive terminal
name - name of the container
hostname - hostname of the container
ubuntu:latest - docker image name
/bin/bash - indicates we wanted to run the bash terminal inside the container
</pre>

List the running containers
```
docker ps
```

Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/8db74c1d-cc72-4d31-b862-8d7710d5e0cc" />

## Lab - Getting inside the running container shell
```
docker ps
docker exec -it ubuntu1 /bin/bash
hostname
hostname -i
exit
```

Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/84f95993-e29d-4617-9095-829180a5afea" />
