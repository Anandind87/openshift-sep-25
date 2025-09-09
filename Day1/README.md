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

## Lab - Stopping a running container
```
docker ps
docker stop ubuntu1

# Listing only running containers
docker ps

# Listing all containers even if they aren't running
docker ps -a
```

Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/b6ee00c0-fc2f-4e70-a17f-d08a8f8a01b8" />

## Lab - Stopping multiple containers with a single command
```
docker ps
docker stop ubuntu2 ubuntu3
docker ps -a
```

Expected output
<img width="1676" height="590" alt="image" src="https://github.com/user-attachments/assets/de119f13-a491-4490-ba3a-3b982ebcbf52" />

## Lab - Starting containers
Starting a single container
```
docker ps -a
docker start ubuntu1
docker ps
```
Starting multiple containers 
```
docker ps -a
docker start ubuntu2 ubuntu3
docker ps
```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/a58db7ae-fb6c-4b43-972a-1ff6730a500f" />

## Lab - Restarting multiple containers without using their names
```
docker ps
docker restart $(docker ps -q)
docker ps
```

Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/184a49c1-6781-4bba-b341-91381f6faea0" />


## Lab - Deleting containers

Delete a single running container gracefully
```
docker ps
docker stop ubuntu1
docker rm ubuntu1
docker ps -a
```

Delete multiple containers forcibly
```
docker ps -a
docker rm -f ubuntu2 ubuntu3
docker ps -a
```

<img width="1676" height="898" alt="image" src="https://github.com/user-attachments/assets/0ecba137-6e97-46d7-b519-db5d6f8c5ef3" />

## Lab - Rename a container
```
docker ps -a
docker rename <existing-name-of-container> <new-container-name>
docker rename ubuntu1 ubuntu1-jegan
```

<img width="1676" height="534" alt="image" src="https://github.com/user-attachments/assets/5e52e81a-f012-4c76-b05d-0aa3b947fe22" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/e57abc87-9670-44f7-bb0f-e8ec5d8f87d9" />

## Lab - Finding detailed information about your container
```
docker inspect nginx1
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/e0bf2f77-6e40-4714-a22f-0a77fa5e1d7b" />

## Lab - Finding IP Address of a container
```
docker inspect nginx1 | grep IPA
docker inspect -f {{.NetworkSettings.IPAddress}} nginx1
```

## Lab - Inspecting docker image
```
docker image inspect mysql:latest
```

## Lab - Accessing containerized application logs
```
docker logs nginx1
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/cb659b40-533b-482f-bf4f-07dd33ed6db1" />

## Lab - Setup a Load Balancer using nginx image
Delete any existing containers
```
docker rm -f $(docker ps -aq)
```

Create 3 web server containers
```
docker run -d --name nginx1 --hostname nginx1 nginx:latest
docker run -d --name nginx2 --hostname nginx2 nginx:latest
docker run -d --name nginx3 --hostname nginx3 nginx:latest
```

List all the 3 web server containers
```
docker ps
```

Find the IP addresses of all 3 web server containers
```
docker inspect -f {{.NetworkSettings.IPAddress}} nginx1
docker inspect -f {{.NetworkSettings.IPAddress}} nginx2
docker inspect -f {{.NetworkSettings.IPAddress}} nginx3
```

Let's create the loadbalancer container
```
docker run -d --name lb --hostname lb -p 8080:80 nginx:latest
docker ps
```


<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/b40f19c1-edd1-4437-8c48-faa6626c8291" />

Copy the nginx.conf file from lb container to local machine
```
docker cp lb:/etc/nginx/nginx.conf .
ls
cat nginx.conf
```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/32989a4d-56bb-419c-a4d1-9ad31ab243f2" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/29e896a6-f618-4b02-ae41-913ca67cdfe3" />

Updated nginx.conf file should look as shown below
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/7182b2b4-1ddc-4465-b007-d0ab2be8afc0" />

We need to copy the updated nginx.conf from local machine to the lb container
```
docker cp nginx.conf lb:/etc/nginx/nginx.conf
```

Restart the lb container to apply the lb config changes
```
docker restart lb
```

Check if the lb container is still running after the lb config changes made
```
docker ps
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/d201769d-6c03-4048-95b3-d96c7ee4f37c" />

Let's customize the response web page on nginx1, nginx2 and nginx3 web server containers
```
echo "Server 1" > index.html
docker cp index.html nginx1:/usr/share/nginx/html/index.html

echo "Server 2" > index.html
docker cp index.html nginx2:/usr/share/nginx/html/index.html

echo "Server 3" > index.html
docker cp index.html nginx3:/usr/share/nginx/html/index.html
```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/ff021a71-f1e3-4709-a575-5ba7894a96fd" />


## Lab - Building a custom docker image

Clone the TekTutor Training Repository
```
cd ~
git clone https://github.com/tektutor/openshift-sep-2025.git
cd openshift-sep-2025/Day1/CustomDockerImage
cat Dockerfile
docker build -t myubuntu:1.0 .
```
Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/68551039-0496-4a1f-91a0-45b1abebb59b" />
