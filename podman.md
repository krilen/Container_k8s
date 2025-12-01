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

- Starting a container,  'podman run ...'  
	Will start a container instance of a container image  

		'podman run registry.redhat.io/rhel7/rhel:7.9'  
		Will start the container instance in attached mode and you will see the output on the screen

		'podman run -d ...'  
		Will start the container instance in deattached mode and it will run in the background

		'podman run --rm ...'
		Will automatically remove the container instance when the conatiner is stopped
	
		'podman run --name db ...'  
		The container instance will have the name "db" to maked is simplear to identify it.
	
		'podman run -p 8080:80 ...'  
		To access a container you must expose them.  
		With the *'-p'* parameter you expose the incoming port to the containers port. In the above example
	 	port 8080 are exposed in podman and it will connect to post 80 on the specified container.


- Stopping container gracefully, *'podman stop ...'*  
	When stopping a container Podman sends a SIGTERM signal to the container to start the cleanup procedure
	before. If the container does not reply to the SIGTERM signal Podman sends a SIGKILL signal to the 
	container to forcefully stop the container by defualt Podmans waits 10 seconds before sending the 
	SIGKILL signal but it can be changed
	
		'podman stop web'  
		Stop the container named "web"
		
		'podman stop 1b982aeb75dd'  
		Stop the container with the specified ID
		
		'podman stop --all'  
		Stops all running containers
		
		'podman stop --time=100 web'
		Change the default defualt gracetime of 10s before sending the SIGKILL signal, but if the 
		container responses as normal it stops as normal, does not wait

	A container that is stops enters an exited state and is not removed unless the flag "--rm" is added
	to the run command. You can see all stopped container with 'podman ps --all'.
	
	A stopped container can be started.
	
	But a container can also be stopped and end up in an exited state becase of other reasons like OOM
	state, some error state or it have finished its work.

- Stopping container forcefully, *'podman kill ...'*  
	Just like stopping a container but you send a SIGKILL signal to the container and the container gets
	stopped forcefullt.
	
		'podman kill web'
		Kills the container direct with a SIGKILL signal instead of a SIGTERM signal.

- Pausing containers, *'podman pause ...'*  
	Both 'podman kill ..' and 'podman kill ...' could eventually send the SIGKILL signal but the 
	'podman pause ...' command suspends att processes in the container with the SIGSTOP signal.
	A container that has beend paused can be unpaused.
	
		'podman pause web'  
		Pause the container named "web"
		
		'podman ps --all'  
		To be able to see container that is in the pause state
		
		'podman unpause web"
		Unpause the container named "web" ans resume it as running.


- Restart container, *'podman restart ...'*  
	Restarts a running container it can also start a stopped container.
	
		'podman restart web'
		Restarts a running container named "web"

- Remove container, *'podman rm ...'*  
	A container that has been stopped can be removed unless the flag "--rm" was used when starting 
	the container. A running container can not be removed it must be stopped unless you use the force
	flag "--force", the contarin is stopped forcefully then removed.
	
		'podman rm web'  
		Remove the stopped the container named "web"
		
		'podman rm 1b982aeb75dd'  
		Remove the stopped the container with the specified ID
		
		'podman rm --all'  
		Removing all stopped containers
		
		'podman rm web --force'
		Forcing the removal of the container even if it is running

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

---

## QUADLETS

Quadlets bridge the integration between systemd and containers by creating a configuration to manage a 
container as a system application.

Systemd is a system and service management tool for Linux operating systems. Systemd uses service unit
files to start and stop applications, or to enable them to start at boot time. Typically, an administrator
manages these applications with the systemctl command.

### Quadlet unit file

A Quadlet Unit file is a systemd unit file but with a custon container section to specify the containers
properties.

	Quadlet unit file
	-----
	[Unit]
	Description=One time container that outputs the contents of the OS release file
	
	[Container]
	Image=registry.access.redhat.com/ubi8/httpd-24:latest
	Volume=/www:/var/www/html:ro
	Entrypoint=/usr/bin/cat
	Exec=/etc/os-release

	[Install]
	WantedBy=multi-user.target

The container section tells in the Quadlet unit file  

1. Tells which image to use (httpd-24:latest)

1. Maps a storage volume (/www -> /var/www/html)

1. Tells the entrypoint (/usr/bin/cat)

1. Adds additional parameters for the entrypoint (/etc/os-release)


The Quadlet unit files use the <serviceName>.container naming pattern and can be stored in the following
paths:

| Rootful                        | Rootless                             |
| ---                            | ---                                  |
| /run/containers/systemd/       | $XDG_RUNTIME_DIR/containers/systemd/ |
| /etc/containers/systemd/       | $XDG_CONFIG_HOME/containers/systemd/ |
| /usr/share/containers/systemd/ | /etc/containers/systemd/users/$(UID) |
|                                | /etc/containers/systemd/users/       |

After adding or modifying unit files, you must use the systemctl command to reload the systemd 
configuration.

	'systemctl --user daemon-reload'

### Managing the Quadlet Service

To manage a Quadlet service, use the systemctl command.

	systemctl --user [start, stop, status, enable, disable] container-web.service

When you use the --user option, by default, systemd starts the service at your login, and stops it at your
logout. You can start your enabled services at the operating system boot, and stop them on shutdown, by
running the loginctl enable-linger command.

	loginctl enable-linger

To revert the operation, use the loginctl disable-linger command.

















