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

### Testing and workflow

An advantage is the ability to scale, a developer can write software and test it locally the deploy it to
with few or no change. This workflow is useful when writing microservices that are small and short lived
that are designed to run spin up and down only when needed. Developers that use containers can take advantage
of Continuous Integration/Continuous Development (CI/CD) pipelines to deploy containers to various environments.

### Stability

By having a container that isolates a applications dependent libraries and the different version a developer
can easy deploy a newer version of the appliaction that has been updated without having to update the host
system. It is all contained inside the container and will not cause a dependecy issue.

For example, a container with a specific version of Python ensures that the same version of Python is
used in every testing or deployment environment.
 
### Multi-Container applications

This application is distributed over severla containers. You can run the containers from the same image for
high availability (HA) replicas, or from several different images.

For example, a developer can create an application that includes a database container that runs separately
from the application's web API container. 

---

## CONTAINER LAYERS

A container image are immutable and layered. Each layer consists of file system differences (diffs). A diff
contains the changes from the previous layer (adding, removing orch modifying file/s).

When a container is started the writable layer is added called *container layer*, this layer that can be read
and written is used for runtime file operations such as wotking files, temp filers and log files. When the
container is deleted this layer is deleted. Meaning that this layer is not suitable for storage, to keep
data beyond a container lifetime (database, ...) a persistant container storage is needed.

---

## CONTAINER REGISTRY

A container image is a packed version of your application with all of its dependecies to be able to run.
A container registry (or simply registry) is used to store container images and to share them if needed,
example of regististires are Quay.io, Red Hat Registry, Docker Hub,  

It is from an external registry that you pull container images to your local storage.

### Red Hat Registry

Redhat distributes containers using two registries

- registry.access.redhat.com: requires no authentication
- registry.redhat.io: requires authentication

But Red Hat has a centrilized serach utility for both registries called "The Red Hat Ecosystem Catalog".
With it you can search for images and get detailed information about an container images.

	https://catalog.redhat.com/software/containers/explore 

With it you get detailed information about an container imae

### Quay.io

Quay.io can be used to store your own custom container images. Login can be done using a RedHat developer
account.

---

## Manage Registries with skopeo

Skopeo is a CLI for working withe container images.  
You can use it in several ways

Skopeo can inspect remote images or transfer images between registries without using local storage. 
The skopeo command uses the transport:image format, such as *docker://remote_image*, *dir:path*, or 
*oci:path:tag*.

- Inspect remote container images.  

		'skopeo inspect docker.io/krilen/hello:v2'
		You get information (metadata) about the remote container image

- Copy a container image between registries.  
	
		'skopeo copy docker://registry.access.redhat.com/ubi9/nodejs-18 docker://quay.io/myuser/nodejs-18'
		Use the skopeo copy command to copy images between registries. The following example copies the 
		registry.access.redhat.com/ubi9/nodejs-18:latest image into the quay.io/myuser/nodejs-18 Quay.io repository.

		'skopeo copy docker://registry.access.redhat.com/ubi9/nodejs-18 dir:/var/lib/images/nodejs-18'
		The following example changes the transport format to download an image into a local director

- Sign an image with OpenPGP keys.
- Convert image format, for example from Docker to the OCI format.

---

## CREATING CUSTOM CONTAINER IMAGES

It is possible to create you own container image. To do that you need to create a "Containerfile" and
in it supply the necessary instructions to build a container image.

Each line in the Containerfile adds another layer to the container image, each layer is stacked on to 
of each other.

**OBS!!!** In Docker they use "Dockerfiles" instead of "Containerfile" they are basically the same but 
"Containerfile" does not associate with any specific container runtime engine.

Each instructions adds some change on top on a container base image. The base image is the minimal needed
to run the application often consisting of  

- Package manager
- Init system
- File system layout
- Preinstalled dependencies and runtime for the type of base image (php, python, mysql, ...)


### Red Hat UBI

For Red Hat container images uses their UBI (universal base image) that comes in different variants, 
see below). Other UBI images provided by Red Hat other runtimes such as Ptyhon and Node.js.
 
Variants of UBI images

- standard  
	This is the primary UBI, which includes DNF, systemd, and utilities such as gzip and tar.

- init  
	Simplifies running multiple applications within a single container by managing them with systemd.

- minimal  
	Thiss image is smaller than the init image and still provides nice-to-have features. This image uses 
	the microdnf minimal package manager instead of the full-sized version of DNF.

- micro
	This is the smallest available UBI because it only includes the bare minimum number of packages. 
	For example, this image does not include a package manager.

### Containerfile instructions

Containerfiles use a small domain-specific language (DSL) consisting of basic instructions for
crafting container images. The following are the most common instructions.

- FROM  
	Sets the base image for the resulting container image. Takes the name of the base image as an 
	argument.

- WORKDIR  
	Sets the current working directory within the container. Instructions that follow the WORKDIR instruction
	run within this directory.

- COPY and ADD  
	Copy files from the build host into the file system of the resulting container image. Relative paths
	use the host current working directory, known as the build context. Both instructions use the working
	directory within the container as defined by the WORKDIR instruction.

	The ADD instruction adds the following functionality:

	- Copying files from URLs.
	- Unpacking tar archives in the destination image.

	Because the ADD instruction adds functionality that might not be obvious, developers tend to prefer
	the COPY instruction for copying local files into the container image.

- RUN  
	Runs a command in the container and commits the resulting state of the container to a new layer
	within the image.

- ENTRYPOINT  
	Sets the executable to run when the container is started.

- CMD  
	Runs a command when the container is started. This command is passed to the executable defined by
	ENTRYPOINT. Base images define a default ENTRYPOINT, which is usually a shell executable, such as Bash.

- USER  
	Changes the active user within the container. Instructions that follow the USER instruction run
	as this user, including the CMD instruction. It is a good practice to define a different user other
	than root for security reasons.

- LABEL  
	Adds a key-value pair to the metadata of the image for organization and image selection.

- EXPOSE  
	Adds a port to the image metadata indicating that an application within the container binds to this
	port. This instruction does not bind the port on the host and is for documentation purposes.

- ENV  
	Defines environment variables that are available in the container. You can declare multiple ENV
	instructions within the Containerfile. You can use the env command inside the container to view
	each of the environment variables.

- ARG  
	Defines build-time variables, typically to make a customizable container build. Developers commonly 
	configure the ENV instructions by using the ARG instruction. This is useful for preserving the 
	build-time variables for runtime.

- VOLUME  
	Defines where to store data outside of the container. The value configures the path where Podman
	mounts persistent volume inside of the container. You can define more than one path to create
	multiple volumes.

Each Containerfile instruction runs in an independent container by using an intermediate image built
from every previous command. This means each instruction is independent from other instructions in
the Containerfile. The following is an example Containerfile for building a simple Apache web server
container:

	Example of a Containerfile
	---
	# This is a comment line #1
	FROM        registry.redhat.io/ubi8/ubi:8.6 #2
	LABEL       description="This is a custom httpd container image" #3
	RUN         yum install -y httpd #4
	EXPOSE      80 #5
	ENV         LogLevel "info" #6
	ADD         http://someserver.com/filename.pdf /var/www/html #7
	COPY        ./src/   /var/www/html/ #8
	USER        apache #9
	ENTRYPOINT  ["/usr/sbin/httpd"] #10
	CMD         ["-D", "FOREGROUND"] #11
	---
	
	Explaination for the above Containerfile
	---
	#1: Lines that begin with a hash, or pound, sign (#) are comments.  
	#2: The FROM instruction declares that the new container image extends the registry.redhat.io/ubi8/ubi:8.6 
		container base image.
	#3: The LABEL instruction adds metadata to the image.
	#4: RUN executes commands in a new layer on top of the current image. The shell that is used to
		execute commands is /bin/sh.
	#5: EXPOSE configures metadata indicating that the container listens on the specified network port
		at runtime.
	#6: ENV is responsible for defining environment variables that are available in the container.
	#7: The ADD instruction copies files or directories from a local or remote source and adds them
		to the container's file system.
	#8: COPY copies files from a path relative to the working directory and adds them to the container's
		file system.
	#9: USER specifies the username or the UID to use when running the container image for the RUN,
		CMD, and ENTRYPOINT instructions.
	#10: ENTRYPOINT specifies the default command to execute when users instantiate the image as a
		container.
	#11 CMD provides the default arguments for the ENTRYPOINT instruction.  
	---



























