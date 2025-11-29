# CONTAINERS

**A container is a standard, portable software package that bundles an application with all its
dependencies, such as libraries and configuration files.**

## GENERAL

A container is an encapsulated process with the dependent libraries it needs to run its program.
The specific libraries are independent from the host OS libaries. Libraries and other things that 
are not spcific to the container is provided by the OS and kernel.  

Example of running a Python program  

Independent  

- The python enviroment itself version (version of python)  
- The specific python files for the program  
- The specific imports (dependencies) that the python program needs

Not independent  

- Kernel or anything needed to support the running python (not python itself)

A container engine creates a union file by merging container image files. Container image is immutable
at runtime the container engine adds a writeable layer for modifications. When the container is remove the
writeable layer is removed.

Containers uses Linux kernel features as namespaces (isolate processes within the container from each other
and from the host system) and cgroups (CPU allocation time and system memory)

Evolved from *'chroot'* to OCI (Open Container Initative) that organization that defines standards for
creating and running containers. It is a standard that that different container enigines should conform
to to make it easier for developers to build and run their containers on different container engine.

Containers can be split into two things  

- Container image  
  Contains the immutable data the defines an application and its libraries (dependencies)
- Container instances  
  Is the running version of the image with resources like networking, storage and other needed necessities
  
A single container image can multiple times create distinct container instances across a single host or
multiple hosts

---

## CONTAINERS vs VIRTUAL MACHINES (VM)

Management sofware is diffrent between containes and VM

- Containers: containerd, docker, podman, ...
- Virtual machines: Xen, KVM, VMware, Hyper-V, ...

|                             | Virtual machines              | Containers                                 |
| ---                         | ---                           | ---                                        |
| Machine-level functionality | Hypervisor                    | Container engine                           |
| Management                  | VM management interface       | Container engine or orchestration software |
| Virtualization level        | Fully virtualized environment | Only relevant parts                        |
| Size                        | Measured in gigabytes         | Measured in megabytes                      |
| Portability                 |Generally only same hypervisor | Any OCI-compliant engine                   |

While the management of a container often comes with the container engine while VMs require additional 
software to be able to manage them. You can also use container orchestrations tools like Openshift, Kubernets, 
Docker swarm, ... to run and manage containers on a scale.

A container (especially that follows OCI) can be run on different container enine a VM can also allwas run
on a specific hypervision and not a different one.

Both VMs and container can scale but fewer resouces are needed for containers. A normal practice when 
running large-scale enviroments is to run containes inside of VMs. This takes advantage of the strong points
in each technology

---

## DEVELOPMENT FOR CONTAINERS

By containerize provides advantages for the development process such as easy testing and deploying.

### TESTING AND WORKFLOW

An advantage is the ability to scale, a developer can write software and test it locally the deploy it to
with few or no change. This workflow is useful when writing microservices that are small and short lived
that are designed to run spin up and down only when needed. Developers that use containers can take advantage
of Continuous Integration/Continuous Development (CI/CD) pipelines to deploy containers to various environments.

### STABILITY

By having a container that isolates a applications dependent libraries and the different version a developer
can easy deploy a newer version of the appliaction that has been updated without having to update the host
system. It is all contained inside the container and will not cause a dependecy issue.

For example, a container with a specific version of Python ensures that the same version of Python is
used in every testing or deployment environment.
 
### MULTI-CONTAINER APPLICATIONS

This application is distributed over severla containers. You can run the containers from the same image for
high availability (HA) replicas, or from several different images.

For example, a developer can create an application that includes a database container that runs separately
from the application's web API container. 

















