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

By adding different options (flags) with commands you can do many things.

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

- *'podman run ...': Will start a container instance of a container image  
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
	With the *'-p'* flag you expose the incoming port to the containers port. The above example port
	8080 are exposed in podman and it will connect to post 80 on the specified container.
	
- *'podman ps'*: See the current runnings container instances
	- ex: *'podman ps -all'*:  
	Will see all running container instances **AND** all instances that are stopped but not deleted/removed.
	. ex: *'podman ps -all' --format=json*:  
	Will see the output in JSON format

### Enviromental variables

You are able to set enviromenatl variables for the container instance by using the *'-e ...'* flag.
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

Managemanet Podman network with the subcommand.

When you do not specify a network for a container 

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

- *'podman network ls'*  
	Lists existing networks and a brief summary of each. Options for this command include various filters
	 and an output format to list other values for each network.

- *'podman network inspect ...'*  
	Outputs a detailed JSON object containing configuration data for the network.

- *'podman network rm ...'*  
	Removes a network.

- *'podman network prune'*  
	Removes any networks that are not currently in use by any running containers.

- *'podman network connect ...'*  
	Connects an already running container to an existing network. Alternatively, connect containers to 
	a Podman network on container creation by using the --net option.

- *'podman network disconnect ...'*  
	Disconnects a container from a network.


