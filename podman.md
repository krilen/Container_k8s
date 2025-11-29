# PODMAN

Podman is an open source tool to manage containers locally. With Podman you can fund, run, build or deploy
OCI containers and container images.

By default Podman is daemonless. A daemon is a process that is always running and is ready for incomming
requests. Other container tools uses daemons to process request, a daemon may require elevated privileges
and creates a security concern. Podman inteacts directly with containers, images and regidstries without
a daemon.

Podman comes in the form of a CLI which is supported by severla OS. Along with the CLI podman provides
two additional ways to interact with your containers and automated processes, the RESTful API and the
GUI Podman Desktop.

---

## INSTALLATION

---

## GENERAL COMMANDS

Command that you can run using *'podman'*

By adding different options (parameter) with commands you can do many things.

To get all options run *'podman --help'*. The help can also be useful with the different options that
you can use *'podman run --help'*.

Example of help

	'podman --help'
	Manage pods, containers and images
	
	Usage:
		podman [options] [command]
	
	Available Commands:
		artifact    Manage OCI artifacts
		attach      Attach to a running container
		...
		run         Run a command in a new container
		...
	---
	'podman run --help'
	Run a command in a new container

	Description:
		Runs a command in a new container from the given image
	
	Usage:
		podman run [options] IMAGE [COMMAND [ARG...]]
	
	Examples:
		podman run imageID ls -alF /etc
		...

- *'podman -v'*: Which version of podman you run

- *'podman pull ...*': Pull a container from a registry to your local machine

	- ex: *'podman pull registry.redhat.io/rhel7/rhel:7.9'*:  
	Pulls from "registry.redhat.io/rehel7" the container named "rhel" with the version (tag) "7.9".  
	It **ONLY** downloads the container image not runs it.
	
- *'podman images'*: See which container images are stored locally on the machine

- *'podman run ...'*: Will start a container instance of a container image  

	- ex: *'podman run registry.redhat.io/rhel7/rhel:7.9'*  
	Will start the container instance in attached mode and you will see the output on the screen
	- ex: *'podman run -d ...'*  
	Will start the container instance in deattached mode and it will run in the background
	- ex: *'podman run --rm ...'*  
	Will automatically remove the container instance when the conatiner is stopped
	- ex: *'podman run --name db ...'*  
	The container instance will have the name "db" to maked is simplear to identify it.
	- ex: *'podman run -p 8080:80 ...'*  
	To access a container you must expose them.  
	With the *'-p'* parameter you expose the incoming port to the containers port. In the above example
	 port 8080 are exposed in podman and it will connect to post 80 on the specified container.
	
- *'podman ps'*: See the current runnings container instances
	- ex: *'podman ps -all'*:  
	Will see all running container instances **AND** all instances that are stopped but not deleted/removed.
	. ex: *'podman ps -all' --format=json*:  
	Will see the output in JSON format

### Enviromental variables

You are able to set enviromenatl variables for the container instance by using the *'-e ...'* parameter.
This will set an enviomental varaible inside the container that can be accessed. By using an env variable
you can add data/infromation to the container without having to rebuild the application.

*'podman run -e HELLO="world" ...'*: Will set the enviromental value with the name "HELLO" with the 
value "world". Any application within the container instance can now access the value by using the
name of the enviomental value.

For example, in Node.js, you can access environment variables by using "process.env.VARIABLE_NAME".

### Podman desktop

Is a Graphical iser interface that can manage and interact with containers in local enviroments. By
default it uses the Podman engine but supports other container engines.

Podman Desktop should be seens as an addition and NOT as an replacement for the CLI. Podman Desktop is
modular and extensible and you can create extensions to provide additional capabilities.

---

## PODMAN (CONTAINER) NETWORKS

If you have several container instances running you can isolate them as needed so they only have access to
only the containers that they need.

Example

- Database container: BO network
- API container: BO and FO network
- Webserver container: FO network

You can now create different networks. Create the BO network and place both the database and API container
in that network. Then create the FO network and place both the API and webserver container in that network.
Now the webserver can reach the API container and the API container can reach the databse container. The
webserver container con NOT reach the databse container.

### Podman network commands

Manage Podman network with the subcommand.

When you do not specify a network for a container Poman uses the *pasta* network mode. A container that
uses the *pasta* network mode can NOT be added to the *bridge* network. Meaning you must add the container to
desired network when the container instance is created, not after.

- *'podman network create ...'*  
	Creates a new Podman network. This command accepts various options to configure properties of the 
	network, including gateway address, subnet mask, and whether to use IPv4 or IPv6.
	- ex: *'podman network create example-net'  
	Creates the network "example-net"  
	
		You can now start a container and attach it to the network
		
		- *'podman run -d --net example-net nginx:latest'*  
		This will attach the container to the "example-net" network
		
		It is also possible to attach the new contaienr to multiple networks
		
		- *'podman run -d --net example-net,redis-net nginx:latest'*  
		The container will be attached to 2 networks.
		
	It is possible to use the isolate option with the default brigde driver. This option isolates the network
	by blocking any traffic from it to any other network, this by using the '-o isolate' parameter.
	
	- ex: *'podman network create -o isolate example-net'*  
	Creates the isolated network "example-net"

- *'podman network ls'*  
	Lists existing networks and a brief summary of each. Options for this command include various filters
	 and an output format to list other values for each network.

- *'podman network inspect ...'*  
	Outputs a detailed JSON object containing configuration data for the network.

- *'podman network rm ...'*  
	Removes a network.
	
	- ex: *'podman network rm example-net'*  
	Removes the network "example-net"

- *'podman network prune'*  
	Removes any networks that are not currently in use by any running containers.

- *'podman network connect ...'*  
	Connects an already running container to an existing network. Alternatively, connect containers to 
	a Podman network on container creation by using the --net option.

- *'podman network disconnect ...'*  
	Disconnects a container from a network.


### Rootful and Rootless Container networks

Podman networks behave differently when you run containers as the root user (rootful containers) compaired
when you run containers as a non-root user, rootless containers.

As rootful containers Podman uses the "podman network" which i pre-configured to attach containers to it.
By default the Podman default networks comes with DNS disabled, without DNS attached containers can reach
other containers using the containers IP address but now use the container names to communicate with each
other. 

If you run a container rootless the nonroot can NOT create network bridge or manage virtual interfaces on
the host. In thsi case Podman creates the required networking interface on the network namespace. By default
Podman does not attache rootless containers to the podamn default network.

If you need want rootless containers to communicate with each oter then you must createa Podman network and
attach the containersto it or attach the containers to the default Podman network.

### Container Domain name resolution

When using network with DNS enabled a containers hostname or alias is the name that is assigned to the 
container. Be aware that this is done from another container attached to the same network.

	'podman network create test-net': Create network
	
	'podman run -d --net test-net --rm --name nginx1 nginx': Create container
	'podman run -d --net test-net --rm --name nginx2 nginx': create other container
	
	'podman exec -it nginx1 /bin/bash': Attach to a the first container
	
	'curl http://nginx2:80': Sent a request to the second container using 'curl'
	
	A reply from the other container by using its name is possible!
	
	No exposed port was added to the container

### Reference External Hosts by name

Whan a container is created Podman adds the host.containers.internal and host.docker.internal hostnames to 
the containers /etc/hosts file by default.

	'podman run -d --rm --name web nginx': Run container
	'podman exec -it web cat /etc/hosts': Check the containers host file
		...
		169.254.1.2	host.containers.internal host.docker.internal
		91.98.232.179	a6366bb5b0be web

You can also manually add the host and IP address to the containers hostf ile by using the parameter
'...--add-host=host:ip'. Thus providing the application inside the ability to resolve a hostname to an 
IP address.

	'podman run --add-host=myhostname:192.168.1.1 example-image'







































