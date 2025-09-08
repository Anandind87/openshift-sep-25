# Day 1

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

## Info - Hypervisor High-Level Architecture
![Hypervisor](HypervisorHighLevelArchitecture.png)
