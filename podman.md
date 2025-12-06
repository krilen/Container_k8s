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

https://docs.podman.io/en/latest/


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

- *'podman images'*: See which container images are stored locally on the machine


	

---

## MANAGE CONTAINERS COMMANDS

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
	flag to filter out desired output, the flag expect a Go template expression as a parameter. The 
	template uses curly braces as delimeter to refrence to elements as fields or keys in a data structure.
	The data structure is seperated with a . and must strat with an upper case.

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
	If the images does not exits locally it will search container registries and if it finds the container 
	image it will pull it down then start the container instance.

		'podman run registry.redhat.io/rhel7/rhel:7.9'  
		Will start the container instance in attached mode and you will see the output on the screen

		'podman run -d ...'  
		Will start the container instance in deattached mode and it will run in the background

		'podman run --rm ...'
		Will automatically remove the container instance when the conatiner is stopped
	
		'podman run --name db ...'  
		The container instance will have the name "db" to maked is simplear to identify it.
		
		'podman run --net fo ...' or 'podman run --network fo ...'  
		Attach the container instance to a specific network named "fo".
	
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

## MANAGE CONTAINER IMAGES COMMANDS

- Pull container images, *'podman pull ...*'  
	Pull a container image from a registry to your local machine.  
	
		'podman pull registry.redhat.io/rhel7/rhel:7.9'  
		Pulls from "registry.redhat.io/rehel7" the container named "rhel" with the tag (version) "7.9".  
		It **ONLY** downloads the container image not runs it.
	
	To pull an image you normally need  
	- Registry URL  
	You can skip the URL to the registry and in Podman you can specify which repositories you want to
	search in
		- User defined registries are placed in  
		~/.config/containers/registries.conf or /etc/containers/registries.conf
		
		- Global file  
		/etc/containers/registries.conf
		
		OBS!!! In Windows the file exists but in another location look for "registries.conf".  
		
		The user-defined file overrides the Global file, if it exists.  
		The order of which registries are in is also the order of which they are searched.  
		It is also possible to block a registry if needed.  
		
		When pulling an image in Podman and it matches the result in several registries you will be 
		prompted to select.
	
	- User or organization
	
	- Image repository (image name)  
	Sometimes the user/organization and image repository (image name) are the same.  
	
	- Image tag (version)  
	If you do not specify a tag you automatically use the tag "latest"  


### Manage registry credentials with Podman

Some registries require you to authenticate before you are able to anything or something specific.  
Normally you get something like *"... unable to retrieve auth token: invalid username/password: 
unauthorized ..."*

To login you use 'podman login ...' command. 

	'podman login registry.redhat.io'  
	After you connect with the registry you are promptet for a username and password.  
	The results are notified at once.

After you are logged in the credentials are stored in auth.json file in a special location.

	${XDG_RUNTIME_DIR}/containers/auth.json file,  
	the ${XDG_RUNTIME_DIR} refers to a specific directory.  
	The credentials are encoded in base64 format.

---

## MANAGING IMAGES

For the complete list of image-related commands, execute 'podman image --help' in a terminal window.

- Image versioning and tags
	Tag is a way for the developer to keep track of the version of the container image. Normally when
	you update the application you need to keep the dependencies and sometimes even the container OS
	up to date. The best way of keeping track is to use tags on your container images.
	
	It is possible to create a new container image with a new name and tag from a local stored image,
	'podman image tag myapp:v23 myapp_v24': Have copied the container image and supplied it with a new
	tag

- Search image, 'podman search ...'
	It is possible for you to search in the CLI for the different registries that you are connected to
	by using 'podman search ...'. You get the full container image address and often a description.
	
	After you found yor image you can either use 'podman pull ...' or 'podman image pull ...' to get
	the container image downloaded to your local storage.  
	A non-root uses that pulls images are stored in "~/.local/share/containers directory".  
	While images pulled by root is stored in "/varlib/containers directory".
	
	To see what container images that exists in iyour local storage use 'podman image ls'. Only a root
	user can see a root downloaded image container.

- Build images, 'podman build ...'
	An image can be build from a "Containerfile". The file must be pointed to when you do the build.
	When you name your container image that you build you must think about which registry you want to
	use otherwise it will stay in you local registry.
	
		'cat Containerfile'  
			FROM nginx:latest
	
		The simplest build from a local stored image
		
		'podman build --file Containerfile --tag my_nginx:v1'  
		Building the image from the Container file but only giving it a name and varsion (tag)
		
		'podman image ls'
			REPOSITORY               TAG         IMAGE ID      CREATED      SIZE
			localhost/my_nginx       v1          d261fd19cb63  4 weeks ago  155 MB
			docker.io/library/nginx  latest      d261fd19cb63  4 weeks ago  155 MB
		
		You can see the new image that was built and the original image that was used
		
		'podman image tag localhost/my_nginx:v1 docker.io/johnnybravo/my_nginx:v1'
		"Copying" the newly built image and tagging it with a real registry url, user, ...
		
		'podman image ls'
			REPOSITORY                      TAG         IMAGE ID      CREATED      SIZE
			docker.io/johnnybravo/my_nginx  v1          d261fd19cb63  4 weeks ago  155 MB
			localhost/my_nginx              v1          d261fd19cb63  4 weeks ago  155 MB
			docker.io/library/nginx         latest      d261fd19cb63  4 weeks ago  155 MB
		
		You can now see the new container image that is ready to be pushed to the registry
	
	Besides inspect the image and is data you can also see how the image has been build and it layers
	by using the "tree" option, 'podman image tree ...'
	
	It makes it possible to see and even identify eack layer and its size.
	
		'podman image tree docker.io/library/nginx:latest' 
			Image ID: d261fd19cb63
			Tags:     [docker.io/library/nginx:latest]
			Size:     155.5MB
			Image Layers
			├── ID: 36d06fe0cbc6 Size: 81.04MB
			├── ID: 40563e6a01a7 Size:  74.4MB
			├── ID: 0b9737d30a90 Size: 3.584kB
			├── ID: b54819a4cde4 Size: 4.608kB
			├── ID: d52725a1d761 Size:  2.56kB
			├── ID: a527a4b607a3 Size:  5.12kB
			└── ID: 36333c84592c Size: 7.168kB Top Layer of: [docker.io/library/nginx:latest]

- Push images, 'podman push ...'
	After a build you should always push an container image to a registry, public or private, to make
	sure that it is accessable. Normally you have to be logged into the registry to complete the push
	
		'podman push docker.io/johnnybravo/my_nginx:v1'
		It is that simple and why you add the registry address to its name

- Inspect images, 'podman image inspect ...'
	You can inspect a contaienr image ang get usefull information from it like, default user, 
	environmental variables, entry-point, cmd, workinddirectory, labels and so on.
	
	Like inspect for ther things you can use the "--format=..." flag to get something specifically.
	
	Normally you use Podman to do the image inpection of the local stored images but you can use 'skopeo'
	tool to do the same instection on remote stored images.
	
- Remove images, 'podman image rm ...' or 'podman rmi ...'
	Container images that are no longer used can be deleted locally.  
	Make sure that the image is not in use in a container instance.
	
		'podman image rm nginx:latest'
		Remove the image, but sometimes that wont work
		
		'podman image rm -f nginx:latest'
		Then you can use force, "-f" to remove the image
		
		'podman rmi httpd:latest'
		Using the "shortcut" to delete an image

	Sometimes when you have built and removed images other images may remain that is not refrenced by
	other images are considered dangling images. These images only takes up space, to clean up you can
	use the prune command with images, 'podman image prune.
	
	It is also possible to remove all images that are not in current use
	
		'podman image prune'
		Removes all dangeling images
		
		'podman image ls'
			REPOSITORY               TAG         IMAGE ID      CREATED      SIZE
			docker.io/library/httpd  latest      c00bfb4edfeb  2 weeks ago  120 MB
			docker.io/library/nginx  latest      d261fd19cb63  4 weeks ago  155 MB
			
		'podman ps -all'
			CONTAINER ID  IMAGE                           COMMAND               CREATED        STATUS        PORTS       NAMES
			3816e2151050  docker.io/library/nginx:latest  nginx -g daemon o...  2 minutes ago  Up 2 minutes  80/tcp      web
		
		'podman image prune -a'
		Removes all dangeling images AND all container images not in use.
		
		'podman image ls'
			REPOSITORY               TAG         IMAGE ID      CREATED      SIZE
			docker.io/library/nginx  latest      d261fd19cb63  4 weeks ago  155 MB

### Multistage builds

The basic build is a simple Containerfile with all of the information.

A multistage build uses multiple "FROM" instructions to create independednt container build processes,
also called stages. Every stage can have different base images och you can copy files between the different
stages. The last containuer build will be the container image that you will use.

By using mutlistage build you can reduce the container image size and only keep the necessary run-time
dependencies.

An example would be that you first build a container with the environment for the 
application with all of dependencies. Than you compile the application and copy the compiled application
to another container. You could also add a stage for testing or insert it in the first stage.

	Containerfile 
	-----
	# Stage 1
	FROM registry.access.redhat.com/ubi8/nodejs-14:1 as builder #1
	COPY ./ /opt/app-root/src/
	RUN npm install
	RUN npm run build #2

	# Stage 2
	FROM registry.access.redhat.com/ubi8/nginx-120 #3
	COPY --from=builder /opt/app-root/src/ /usr/share/nginx/html #4
	----
	
	Explaination for the above Containerfile
	---
	#1: Define the first stage with an alias. The second stage uses the builder alias to reference
		this stage. Uses a Node.js contariner image.
	#2: Build the application.
	#3: Define the second stage without an alias. It uses the ubi8/nginx-120 base image to serve 
		the production-ready version of the application. Uses an nginx container image.
	#4: Copy the application files to a directory in the final image. The "--from" flag indicates
		that Podman copies the files from the builder stage.
	-----

The above example in the Container files uses 2 stages because of the hugh amount of packages that
gets installer during 'npm install'. After the build is complete in stage 1 container only what is
in "/opt/app-root/src/" is moved into the stage 2 container. 

---

## EXPORTING and IMPORTING

### Containers (running)

It is possible to export a container as a tar file on the local machine there after you can move it 
as any other file. The export creates a snapshot of the container but in suashes the image layers into
a single layer and removes the metadata from the image. The export must be specified to an output the
tar file by using "-o" or "--output".

After that the tar file can be used to import as a container image.

	'podman ps -all'
		CONTAINER ID  IMAGE                           COMMAND               CREATED         STATUS         PORTS       NAMES
		3816e2151050  docker.io/library/nginx:latest  nginx -g daemon o...  46 minutes ago  Up 46 minutes  80/tcp      web

	'podman export -o nginx.tar web'
	Export the running container named "web" as the tar file "nginx.tar" to the locla file system
	
	'podman image ls'
		REPOSITORY               TAG         IMAGE ID      CREATED      SIZE
		docker.io/library/nginx  latest      d261fd19cb63  4 weeks ago  155 MB
		
	Current container images
	
	'podman import nginx.tar newnginx:e01'
	Import the exported container as a container image named "newnginx:e01"
	
	'podman image ls'
		REPOSITORY               TAG         IMAGE ID      CREATED        SIZE
		localhost/newnginx       e01         f0f5b89aca9d  6 minutes ago  154 MB
		docker.io/library/nginx  latest      d261fd19cb63  4 weeks ago    155 MB

	The imported container images now exits as a localhost image. You can use 'podman image tag ...'
	to change to a registry and a different name.

### Container images

To export and import a container image is more or less the same way as a container instance. You use
"save" and "load" instead. The big diffrence is that the image layers does not get squashed and the 
metadata is kept.

The downside that you can not specify another image name when you import it, it uses the name when the
container image had when it was exported. Therefore it may override a image with the same name, be aware.

	'podman image ls'
		REPOSITORY               TAG         IMAGE ID      CREATED        SIZE
		localhost/newnginx       e01         f0f5b89aca9d  6 minutes ago  154 MB
		docker.io/library/nginx  latest      d261fd19cb63  4 weeks ago    155 MB
		
	Current container images
	
	'podman save --output nginx2.tar localhost/newnginx:e01'
	Save the container image to a tar file named "nginx2.tar"
	
	'podman load --input nginx2.tar'
	Importing the container image 

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

If you have several container instances running you can isolate them as needed so they only have access 
to only the containers that they need.

Example

- Database container: BO network
- API container: BO and FO network
- Webserver container: FO network

You can now create different networks. Create the BO network and place both the database and API container
in that network. Then create the FO network and place both the API and webserver container in that network.
Now the webserver can reach the API container and the API container can reach the databse container. 
The webserver container con NOT reach the databse container.

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
		
	It is possible to use the isolate option with the default brigde driver. This option isolates the 
	network by blocking any traffic from it to any other network, this by using the '-o isolate'
	parameter.
	
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
the host. In thsi case Podman creates the required networking interface on the network namespace. By 
default Podman does not attache rootless containers to the podamn default network.

If you need want rootless containers to communicate with each oter then you must createa Podman network 
and attach the containersto it or attach the containers to the default Podman network.

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

Whan a container is created Podman adds the host.containers.internal and host.docker.internal hostnames
to the containers /etc/hosts file by default.

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

A containers network namespace is isolated mening it is only accessable from other containers in the 
podman network. If the appliaction inside of a container needs to be accesses from the outside a port 
forward needs to be setup from the outside to the container.

By using the parameter "-p host_port:container_port" the fowrarding is done.

	'podman run -d -p 8080:80 nginx'  
	From the outside using port 8080 you can access the containers port 80.

By default you assign the broardcast address of the host machine (0.0.0.0) meaning all networks that
the host is connected to. This can be avided by specifying an IP address.

	'podman run -d -p 127.0.0.1:8080:80 nginx'  
	Now only localhost can acces the container using port 8080.

### List port mappings

By using the option "port" you can list the ports for a container

	'podman port web'  
	List all ports for the container named "web"
	
	'podman port --all'
	List all containers and their ports


### Networking in containers

Containes in Podman networks are all assigned private IP addresses for each network. Other containers
are able to make requests to the IP address. To get a specific IP address from a container you must
use the option "inspect"

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

Some container wont allow you to interact with them like above usning bash. And some container might 
not even have 'cat' installed so you are able to see what is inside of the files of the container.

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

	'systemctl --user [start, stop, status, enable, disable] container-web.service'

When you use the --user option, by default, systemd starts the service at your login, and stops it at
your logout. You can start your enabled services at the operating system boot, and stop them on shutdown,
by running the loginctl enable-linger command.

	'loginctl enable-linger'

To revert the operation, use the loginctl disable-linger command.

---

## PODMAN SECRETS

A secret is a blob of sensitive information that is needed by the container during run-time like password,
username, keys, ... After you seperatly create the secret you will instruct the container to make it
available for the appliaction when the container start, example could be username and password for a db.
You use 'podman secret ...' command to work with the SECRETS.  
The secret entries are stored locally on the machine

### Managing Podman secrets

- Create secrets, 'podman secret create ...'
	Podman supports different drivers to store secrets by using the "--driver" or "-d" you can select
	one of the supported drivers.
	
	- file (default): Stores the secret in a read-protected file
	- pass: Stores the secret in a GBG-encrypted file
	- shell: Manages the secret storage by using a custom script.

	Example on how to create a Podman secret
	
		'printf "secretpass" | podman secret create secret1 -'
		The secret "secretpass" will be stored in the secret named "secret1"
		
		'echo -n mysecret > ./secret'
		'podman secret create secret2 ./secret'
		Place the secret i a file, "secret", then use this file to create the secret, "secret2"
		
		'podman secret create --driver=pass my_secret ./secret.txt.gpg
		Using the same file again with the secret and creating a GPG encrypted file, "secret.txt.gpg"

- List secrets, 'podman secret ls'  
	Listing the current secrets in the system to which driver and when the secret was created and updated.
	
		'podman secret ls'
			ID                         NAME        DRIVER      CREATED         UPDATED
			e941c1956a33ba808a8b0b3a4  secret1     file        56 minutes ago  56 minutes ago	
	
- Replace a secret, 'podman secret create --replace ...'  
	Used to replace a current existing secret.
	
- Remove secrets, "podman secret rm ..."  

		'podman secret rm secret1'
		Remove the secret named "secret1"

### Using secrets in Containers

After a secret has been created you can use it in a container by using the flag "--secret" and the name
of the secret that you would like to use. When you use the secret it is placed on a tmpfs volumenand
get mounted inside of the container in the "/run/secrets" directory as a file based on the name of the
secret. The secret will be readable by any application.

	'printf "MYp@ssw0rd" | podman secret create password1 -'
	Create a secret with the name "password1"
	
	'podman run -it --secret password1 container:cmd /bin/bash'
	Start a container with the secret and get a shell prompt to it (only to verify the password).
	
	'cat /run/secrets/password1' -> "MYp@ssw0rd"
	The secret has been placed in a file under "/run/secrets/" with the name of the secret, "password1"

Podman never gets stored in the container itself, neither does 'podman commit' nor "podman export" 
command copy the secret to an image or tar file.
	
---

## Rootless Podman

Before containers a single application was often deployed in its own virtual machine with its own OS.
There could be several virtual machines running on a single physical machine (host machine). Each of 
the virtual machines had there own kernel seperated from the host machines kernel.  
One host machine with it own kernel could run several virtual machines with there own kernel and single
application.

When containers came along it encourages to run each appliaction in it own container. But each containers
processes uses the kernel of the host machine. Often the host machine now due to lower resource requirment
could run more container than virtual nachines and therefore more applications.

The downside is that your where to exploit an application and  gain superuser privilege on the host
machine you could affect a large-scale outahe. Therefore you must strive to create and maintain appliactions
that uses the principal of least privilege which might minimize the affect if hacked.

### Analyzing Rootless Containers

Rootless containers or unprivileged containers are containers that do not require administrator privileges.

A container is rootless ONLY when it meets the followin conditions

- The containerized process does not use the root user, which is a special privileged user in Linux
	and UNIX systems. Such a user is for administrative purposes and has the ID 0.
- The root user inside of the container is not the root user outside of the container.
- The container runtime does not use the root user. For example, if your container runtime runs as
	the root user, then containers managed by such runtime are not rootless containers.

Podman starts each container as a new process the runtime does not require elevated privileges. The 
Podman process exists after creating the container. Then the container process ID attaches to the 'systemd'
parent Process ID

#### Other requirements for rootless containers
Depending on the OS Podman may require host OS setup to run rootless containers.

Prerequisite setup listing

- cgroups v2  
	Cgroup v2 is a Linux kernel feature that Podman uses to limit container resource use without
	requiring elevating privileges.  
	Podman on Red Hat Enterprise Linux 9 (RHEL 9) uses cgroup v2 by default, with the crun container
	runtime implementation
	
- pasta  
	Podman uses the pasta command from the passt package to implement rootless networking for unprivileged
	network namespaces.

- fuse-overlayfs  
	Podman uses the fuse-overlayfs package to manage the Copy-On-Write (COW) file system. Though Podman
	does not require this package, Red Hat recommends using it for performance reasons. COW file system
	is discussed later in the course.

- More: https://github.com/containers/podman/blob/v5.2.2/docs/tutorials/rootless_tutorial.md


#### Changing the Container User

When a Containerfile is create the user tends to be root, because you may require elevated privileges
for certain operations, like installing packages or making configuration.

But you should when creating your own container image make sure that root user is not used

	Containerfile
	-----
	FROM registry.access.redhat.com/ubi9/ubi
	CMD ["python3", "-m", "http.server"]
	-----
	
	'podman build -t myhttp --file Containerfile'
	Build the container image from a Red Hat base image
	
	'podman run --rm myhttp:latest id -> "uid=0(root) gid=0(root) groups=0(root)"
	Because of the container image uses root to start the HTTP server

The above container image is a security risk since the root is the user. If a hacker exploits the application
and get access to the container and could thefrore after further exploits escape the containerized environmet
into the host system.

Therefor a change to the container image might be a good idea.

	Containerfile
	-----
	FROM registry.access.redhat.com/ubi9/ubi

	RUN adduser \
	   --no-create-home \
	   --system \
	   --shell /usr/sbin/nologin \
	   python-server

	USER python-server

	CMD ["python3", "-m", "http.server"]
	-----
	
	The RUN instruction runs as root user because it precedes the USER instruction
	
	'podman build -t myhttp:rootless --file Containerfile'
	Build the container image from a Red Hat base image with a special user
	
	'podman run --rm myhttp:rootless id -> "uid=998(python-server) gid=998(python-server) groups=998(python-server)"
	Because of the container image uses now uses "python-server" user to start the HTTP server

Now the container will start the same process but without elevated privileges which will make it harder
for the hacker to exploit vulnerability and access the host system. The appliaction might be hacked that
is why you always need to keep it updated.


#### Explaining User Mapping

When a hacker gains access to the container file system by a expliot the root inside of the container
is the same root outide of the system. Meaning if the hacker can escape the container isolation they
have elevated privileges on the host system and can cause damage.

Podman maps user inside of the container to unprivileged users on the host system by using subordinate
 ID ranges. Podman defines the allowed ID ranges in the /etc/subuid and /etc/subgid files.

![From REDHAT Academy](podman/user_mapping.svg)

Example

	'cat /etc/subuid /etc/subgid'
		student:100000:65536
		student:100000:65536
	
With the above information it means that the "student" user can allocate 65536 user IDs starting with
the ID 100000. This leads to the HostUserID = 100000 + ContainerUserID -1 mapping, where user ID 1 in
a container is mapped to the host user ID 100000 and so on.

The user ID 0, root, is an exception because the root user maps to the user ID that started the container.
For example, if a user with the user ID 1000 starts the container that uses the root user, then the root
user maps to the host user ID 1000.

The files /etc/subuid /etc/subgid  must exists before defining the subordinate ID ranges with the 'usermod'.

	'sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 student'
	To generate the subordinates ID range using the 'usermod'
	
	'podman system migrate'
	After the rage is defines you must execute the migration for the new subordinate ID ranges to
	take affect.

Change in /etc/subuid /etc/subgid ???

Now when a container is running (sfter any restart after the migaration) you can see the new mapping
that differ from the inside and outside of a running container

	podman run --rm -it registry.access.redhat.com/ubi9/ubi /bin/bash'
		[root@73a87c800d5d /]# id -> "uid=0(root) gid=0(root) groups=0(root)"

	Inside of the container I am root with User Id and 0
	
	'podman top 73a87c800d5d huser user'
		HUSER       USER
		1000        root
		
	Outside of the running container user ID 1000 is using the same container.

Shows that the user inside the Container, root, is mapped to user ID 1000 on the host system.

When you execute a container with elevated privileges on the host machine, the root mapping does not
take place even when you define subordinate ID ranges.

	'sudo podman run --rm -it registry.access.redhat.com/ubi9/ubi /bin/bash'
		[root@ded563b9e42b /]# id -> "uid=0(root) gid=0(root) groups=0(root)"
		
	Same as before I am root with user Id 0
	
	'sudo podman top ded563b9e42b huser user'
		HUSER       USER
		root        root
		
	Outside of the running container urer root is runnin


#### Limitations of Rootless Containers

There are limitations on running rootless containers that make certin applications unsuitable to be 
containerized as rootless containers.

List of limitation of rootless containers.

- Non-trivial Containerization  
	The application may require the root user, sependet of the application architecture it might not
	be suitable for rootless containers or might require a deeper understanding of containerize.  
	An example would be an application such as Nginx or HTTPd start a bootstrap process and then spawn
	new process with non-privileged user which interacts with external users.
	
- Required Use of Privileged Ports or Utilities
	Rootless containers can not bind to privileged port such as 80 or 443. It is recommended to use 
	privileged port and used port forwarding insted. If neede to use you can configure the unpriviledge
	port range.
	
		'sudo sysctl "net.ipv4.ip_unprivileged_port_start"' -> "net.ipv4.ip_unprivileged_port_start = 1024"
		By default the range starts at port 1024
		
		'sudo sysctl -w "net.ipv4.ip_unprivileged_port_start=79"'
		Setting a new start to the unpriviledge port range, can now start binding at port 80 or higher.

	Rootless container can not use utilities that require the root user such as the ping utility. Because
	the ping utility requires elevated privileges to establish raw sockets which is an action that require
	the "cap.ipv4.ping_group_range" kernel parameter.

		'sudo sysctl -w "net.ipv4.ping_group_range=0 2000000"'
		Setting the kernel parameter.

---

## PERSISTENT DATA

Storing data persistently Poman uses external mounts by using *volumes* and *bind* mounts.

This is useful for the following reason

- Persistence  
	Mounted data are safe during container deletions. Other data in the container in not accessible
	after container deletions.
- Use of Host File System  
	Mounted data does not use the COW file system. Also write-heavy containerized processes can write
	data to a mount without the limitations of the COW file system for write operations.
- Ease of sharing
	Mounted data can be shared across multiple containers at the same time, one contaiiner can write
	to the mount while another read from it. Mounts are not limited to the host file system they can
	be hosted over network (NFS protocol as an example).  
	
Volumes are data mounts managed by Podman.  
Bind mounts are data mounts managed by the user.  
Both mounts uses the flag "-v" or "--volume" with a parameter.

	'... --volume /path/on/host:/patch/in/container:OPTIONS ...'
	The OPTIONS part is optional. Path are specified by their absolute path or as the relative
	path accordingly to the working directory

You can also use the flag "--mount" with paramters since this provides a way for a more explicit
way of specifying the mount.

	'... --mount type=TYPE,source=/path/on/host,destination=/path/in/container ...'
	
	'... --mount "type=volume,source=html-vol,destination=/server"
	Doing a volume mount named "html-vol" attach it to "/server"

The different TYPES that the "--mount" can use is

- bind for bind mounts, managed by users
- volume for volume mounts, managed by Podman
- tmpfs for creating memory-only mount.

The "--mount" flag is the preferred way of mounting directories in a container, the "-v" or "--volume"
is the older but stil used way. 


### Bind mount

Often these mounts are used for testing or for mounting of specific environmental files since they
are managed by the user.

A bind mount can exists anywhere on the host system.

	'podman run -d --rm -p 8080:8080 --volume /www:/var/www/html:ro registry.acc...:latest'
	This will mount the "/www" directory on the host machine to the "/var/www/html" directory in the
	container with the read-only option.
	
#### Troubleshooting Bind mounts

When you use bind mounts you must configure file permissions and SELinux access manually. SELinux is
an additionally security mechanism used by Red Hat and oter Linux distributions.

When looking at the following mount example

	'podman run --rm -d --name web -p 8080:8080 --volume /www:/var/www/html registry.access.redhat.com/ubi8/httpd-24:latest'
	
	'podman exec web ls -la /var/www/html' ->
		ls: cannot open directory '/var/www/html': Permission denied
		
	'podman exec web ls -la /var/www/' ->
		total 20
		drwxr-xr-x. 4 default root   4096 Nov 27 20:23 .
		drwxr-xr-x. 1 root    root   4096 Nov 27 20:23 ..
		drwxr-xr-x. 2 default root   4096 Jul 28 16:04 cgi-bin
		drwxr-xr-x. 2 nobody  nobody 4096 Dec  6 16:23 html
		
	'podman stop web'

By default the Httpd process will have insufficient permission to access the "/var/www/html" directory.
This can be a file permission or an SELinux issue.

The 'podman unshare ...' command executes the provided linux command in a new namespace. The process
maps the user IDs as they are mapped in a new container which is usefule for toubleshooting user permissions.
The 'podman unshare ...' can be used to check and even fix issues with the filesystem.

	'podman unshare chgrp -R root /www'
	Change the group recursive on a directory

To troubleshoot a file permission issue use the 'podman unshare ls -l' command to execute the 'ls -l' command.
 
	'ls -l /www/' -> 
		total 4
		-rw-r--r--. 1 root root 72 Dec  6 17:23 index.html
	'ls -ld /www/' ->
		drwxr-xr-x. 2 root root 4096 Dec  6 17:23 /www/
	
	'podman unshare ls -l /www' -> 
		total 4
		-rw-r--r--. 1 nobody nobody 72 Dec  6 17:23 index.html
	'podman unshare ls -ld /www' ->
		drwxr-xr-x. 2 nobody nobody 4096 Dec  6 17:23 /www

The above example tells that the "/www" directory and its files.

- The file "index.html" provides the read permission to all uses
- The directory "/www" is owned by the nobody user and the nobody group in the new namespace
- The direcory "/www" provides execute permission for all users, which grants all users access to
	the directory content.

The above tells that the file and directory permissions are correct in the bind mount.

To troubleshoot the SELinux permission issue inspect the "/www directory SELinux configuration by
running the ls command with the "-Z" flag.

	'ls -Zd /www' -> 
		unconfined_u:object_r:default_t:s0 /www

Tells that the directory shows the SELinux context label *"unconfined_u:object_r:default_t:s0"*.  
A container must have the "container_file_t" SELinux type to have access to the bind mount.

To fix the SELinux configuration add the ":z" or ":Z" option to the mount

- ":z" (lower case z): lets different containers shareaccess to a bind mount
- ":Z" (upper case Z): provides the container with the exclusize access to the bind mount.

Testing again

	'podman run --rm -d --name web -p 8080:8080 --volume /www:/var/www/html:z registry.access.redhat.com/ubi8/httpd-24:latest' ->
		Error: lsetxattr(label=system_u:object_r:container_file_t:s0) /www: operation not permitted
	
	SELinux error!!
	
	Fixing the issue with the SELinux:
	------
	If the directory /www resides on a file system that supports XATTR, set a directory SELinux 
	context that corresponds to the podman volume flag :z
		
	'sudo chcon -R system_u:object_r:container_file_t:s0 /www'
	-----
	
	'ls -Zd /www' ->
		"system_u:object_r:container_file_t:s0 /www"
	
	'podman run --rm -d --name web -p 8080:8080 --volume /www:/var/www/html:z registry.access.redhat.com/ubi8/httpd-24:latest'
	Now you are able to run the container with a bind mount.

**OBS!!!** The SELinux fix and how to handle it is outside the scope of this document **OBS!!!**

### Volume mounts

Volumes let Podman manage the data mounts, use volumes to ensure consistent mount behavior across system.
Also use volumes for advanced uses like mounting a remote volume over NFS.

You can manage volumes by using the 'podman volume ...' command.

	'podman volume ls' ->
		DRIVER      VOLUME NAME
		
	List the created volumes, nothing exists
	
	'podman volume create http-data'
	Create the volume "http-data"
	
	'podman volume ls' ->
		DRIVER      VOLUME NAME
		local       http-data

	'podman volume inspect http-data' -> 
		"Name": "http-data",
		"Driver": "local",
		"Mountpoint": "/home/student/.local/share/containers/storage/volumes/http-data/_data",
		"CreatedAt": "2025-12-06T19:27:40.240143067+01:00",
		...
	
	Doing an inspect on the created volume and you can see where it is locally storred.

For rootless containers, Podman stores tlocal volume data in the 
$HOME/.local/share/containers/storage/volumes/ directory.

To mount a volume to a container instance is simple. Becase Podman is manages the volume there is no
need to configure SELinux permissions.

	'podman run --rm -d --name web -p 8080:8080 --volume http-data:/var/www/html registry.access.redhat.com/ubi8/httpd-24:latest'
	'... --volume http-data:/var/www/html ...': map the volume "http-data" to "/var/www/html"

Other podman volume commands

- 'podman volume prune': remove unused volumes

#### Exporting and Importing Data with Volumes

It is possible to import data from a tar archive into an existing Podman volume by using the 'podman volume import ...'
command. The volume must exist for the import to be possible.

	'podman volume create http-data2'
	'podman volume import http_data web_data.tar.gz'
	
You can also export data from an existing Podamn volume san save it as atar archive on the local machine,
'podman volume export ...'

	'podman volume export http-data --output web_data.tar.gz'

### Storing Data with a tmpfs Mount

There are applications that can not use the default COW file system in a specific directory for performence
reasons but use persistence or data sharing for that directory.

In those cases you can use the tmpfs mount type which means that the data in a mount is shortlived but
does not use the COW file system.

	'podman run ... --mount type=tmpfs,tmpfs-size=512M,destination=/var/lib/pgsql/data ...'










