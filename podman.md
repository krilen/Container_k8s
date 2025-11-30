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

## PODMAN HELP

Command that you can run using 'podman ...'

By adding different options and flags (with parameters sometimes) you can do many things.

To get all options run 'podman --help'. 

The help can also be useful with the different suboptions that you can use 'podman *option* --help'.

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
		
	Help using the sub option "run"
	
	'podman run --help'
	Run a command in a new container

	Description:
		Runs a command in a new container from the given image
	
	Usage:
		podman run [options] IMAGE [COMMAND [ARG...]]
	
	Examples:
		podman run imageID ls -alF /etc
		...

A flag ("*-x*" or "*--xxx*") is something to help the command sometimes you must supply a parameter,
'podman ... -p 8080:8080 ...', the "8080:8080" is the parameter to the flag "-p". The single "-" can
hold many flags without a parameter while the double "--" is a multi character option. Sometimes both
exists for the same thing.

---

## PODMAN GENERAL COMMANDS

- *'podman -v'*: Which version of podman you run using the 

- *'podman pull ...*': Pull a container from a registry to your local machine

	- ex: *'podman pull registry.redhat.io/rhel7/rhel:7.9'*:  
	Pulls from "registry.redhat.io/rehel7" the container named "rhel" with the version (tag) "7.9".  
	It **ONLY** downloads the container image not runs it.
	
- *'podman images'*: See which container images are stored locally on the machine


	

---

## MANAGE CONTAINERS

Basic operations that can be used to manage containers. 

- Listing containers, *'podman ps'*

		'podman ps'  
		See the current runnings container instances  
		
		'podman ps --all'  
		Will see all running container instances and stopped instances but 	
		not deleted/removed instances.
		
		'podman ps --all --format=json'  
		Will see the output in JSON format

- Inspect containers, *'podman inspect ...'*  
	Meaning retrieving the full information of a container. The return will be in JSON format and contain
	information about different aspects of the container, networking, CPU usage, environment variables, ...
	
	The JSON format output is very long and sometimes hard to read. It is possible to use the "--format"
	flag to filter out desired output, the flag expect a Go template expression as a parameter. The template
	uses curly braces as delimeter to refrence to elements as fields or keys in a data structure. The data
	structure is seperated with a . and must strat with an upper case.

		'podman inspect web'  
		Get the information about a container named "web" (in JSON format)
		
		'podman inspect 7763097d11ab'  
		Get the information about a container using its id (in JSON format)
		
		'podman inspect --format="{{.State.Status}}" web' -> "running"  
		Get the currect state of the "web" container
		
		'podman inspect --format="{{.State.StartedAt}}" web' -> "2025-11-30 17:49:08.001890395 +0100 CET"  
		Get when the container was started

!!!! STOPPING CONTAINERS!!!


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


---

## Environment variables

You are able to set environmental variables for the container instance by using the *'-e ...'* parameter.
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

The default network name is "podman" and by default DNS is disabled.

	podman network inspect podman | grep dns_ -> "dns_enabled": false,

When you create another network the DNS is enabled

	'podman network inspect fo | grep dns_' -> "dns_enabled": true,  
	The DNS for network "fo" is enabled


If you run a container rootless the nonroot can NOT create network bridge or manage virtual interfaces on
the host. In thsi case Podman creates the required networking interface on the network namespace. By default
Podman does not attache rootless containers to the podamn default network.

If you need want rootless containers to communicate with each oter then you must createa Podman network and
attach the containersto it or attach the containers to the default Podman network.

### Container Domain name resolution

When using network with DNS enabled a containers hostname or alias is the name that is assigned to the 
container. Be aware that this is done from another container attached to the same network.

The default network 

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

---

## Port Forwarding

A containers network namespace is isolated mening it is only accessable from other containers in the podman
network. If the appliaction inside of a container needs to be accesses from the outside a port forward
needs to be setup from the outside to the container.

By using the parameter "-p host_port:container_port" the fowrarding is done.

	'podman run -d -p 8080:80 nginx'  
	From the outside using port 8080 you can access the containers port 80.

By default you assign the broardcast address of the host machine (0.0.0.0) meaning all networks that the
host is connected to. This can be avided by specifying an IP address.

	'podman run -d -p 127.0.0.1:8080:80 nginx'  
	Now only localhost can acces the container using port 8080.

### List port mappings

By using the option "port" you can list the ports for a container

	'podman port web'  
	List all ports for the container named "web"
	
	'podman port --all'
	List all containers and their ports


### Networking in containers

Containes in Podman networks are all assigned private IP addresses for each network. Other containers are
able to make requests to the IP address. To get a specific IP address from a container you must use the
option "inspect"

	podman inspect web -f "{{.NetworkSettings.Networks.fo.IPAddress}}"  -> 10.89.0.2
	Will get the IP address of the container "web" that is in the network "fo"

---

## START PROCESSES IN CONTAINERS

Using the exec option to start new processes in a running container.  
'podman exec [options] container_name_or_id [command ...]': For a particulare container 

	'podman exec httpd cat /etc/httpd/conf/httpd.conf': print the "http.conf" file from "httpd" container

With the "exec" options you can do several things

- "--env" or "-e": To specify an environment variable.

	- ex: 'podman exec -e ENVIRONMENT=dev -l env'
	Adds the environmental varable to the lastest container ("-l", see below) and prints the variables.
	But to make it permenent add the environmental varaibales when starting the container

- "--interactive" or "-i": To instruct the container to accept input.

- "--tty" or "-t":  to allocate a pseudo terminal.

- "--latest" or "-l to execute the command in the last created container.  
	Not availbe when using Podman remote client, include runing Podman machine on macOS and Windows


### Interactive sessions in containers

When using the parameter "-i" you are able to interact with a container. You are able to run commands
but in the whole it does not work. You get feedback from he simple commands.

When using the parameter "-t" an pseudo terminal opens to the container but you are not able til interact.

The magic happend when there parameters are used together.

	'podman exec -i web pwd' -> "/": You get back your current place
	'podman exec -il ls /usr' ->
		bin
		games
		...
		src
	Get what is in "/usr" from the latest created container
	
	'podman exec -t web /bin/bash': the pseudo terminal open but you can not do anytning
	
	'podman exec -it web /bin/bash': It is now possible to interact using the pseudo terminal using bash.

### Copy files in and out of containers

Some container wont allow you to interact with them like above usning bash. And some container might not
even have 'cat' installed so you are able to see what is inside of the files of the container.

Perhaps you need to modify a file, remember that the chane is not permenent.

To get access to log files and oter thing the only way might be to do a copy. You are able to copy from
a container to the host system and from the host system to a container.

	'podman cp web:/var/log/messages .'  
	Copy the file "/var/log/messages" from the container "web" to the current place on the host system
	
	'podman cp web:/var/log/messages /tmp/'  
	Same but to the "/tmp" folder
	
	'podman cp config web:/app/'  
	Copy the local file "config" to "/app/" fodler in the container "web"
	
	
































