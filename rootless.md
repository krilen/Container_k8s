# ROOTLESS

Working with rootless Podman from Red Hat Academy.

Probably does NOT work outside Red Hat Academys lab but it is good info.

---

## Instructions

1. Explore and build the Gitea container image.

	A. Explore the Containerfile file. Note that the image creates and uses the default user.
	
		Containerfile
		-----
		FROM registry.ocp4.example.com:8443/redhattraining/podman-gitea-base:ubi9

		ENV GITEA_WORK_DIR=/var/lib/gitea/ \
			GITEA_PORT="3030" \
			HOME="/opt/app-root"

		# Configure gitea to run as any user with the root group
		# This is necessary when deploying on OpenShift
		RUN mkdir -p /var/lib/gitea/{custom,data,log} && \
			mkdir /opt/app-root && \
			chgrp -R 0 /var/lib/gitea/ && \
			chmod -R g=u /var/lib/gitea/ && \
			mkdir /etc/gitea && \
			chgrp -R 0 /etc/gitea && \
			chgrp -R 0 /opt/app-root && \
			chmod -R g=u /etc/gitea && \
			chmod -R g=u /opt/app-root && \
			useradd \
			  --uid 1001 \
			  --system \
			  --gid 0 \
			  --home-dir /opt/app-root \
			  --shell /sbin/nologin \
			  --comment "Default Application User" \
			  default

		USER 1001
		WORKDIR /opt/app-root
		EXPOSE ${GITEA_PORT}

		CMD gitea --port ${GITEA_PORT}
		---

	B. Build the container image with the localhost/gitea tag.

		'podman build -t gitea .'

	C. Repeat the last command for the root user. Use the double exclamation mark (!!) to refer to the previously executed command.

		'sudo !!'
	
	This guided exercise requires that you use the gitea image to start a container by using the root user in a later step. Consequently, because the gitea image is local to your environment, you must build the gitea image for the root user. Users do not share local images by default.

1. Backing up /etc/subgid and /etc/subuid (move them to a secure place wit another name as a backup)

1. Start the containerized Gitea process.

	A. Start a container that uses the gitea image in rootless mode.

		'podman run --rm -p 3030:3030 gitea' ->
		ERRO[0000] cannot find UID/GID for user student: cannot read subids - check rootless mode in man pages.
		WARN[0000] Using rootless single mapping into the namespace. This might break some images. Check /etc/subuid and /etc/subgid for adding sub*ids if not using a network user
		Error: OCI runtime error: crun: cannot setresuid to 1001: Invalid argument

	Podman fails to start the container because the image uses multiple users and Podman does not have enough ID mapping to map the user and group IDs inside the container to non root users and group IDs on your system.

	B. Start a container that uses the gitea image as the root user.

		'sudo podman run --name root-gitea -p 3030:3030 --rm gitea'
		
	C. In a new terminal, verify the user and group ID mapping.

		'sudo podman exec root-gitea cat /proc/self/uid_map /proc/self/gid_map'
			0          0 4294967295
			0          0 4294967295

	When you start the container as root, Podman uses your system's root user for the root user inside the container. Consequently, if an attacker gains root permissions in your container, then they can potentially access your host system.

	D. Stop the root-gitea container.

		'sudo podman stop root-gitea'
		
1. Provide the subuid and subguid ID ranges.

	A. In the Gitea terminal, create the /etc/subuid and /etc/subguid files.

		'sudo touch /etc/{subuid,subgid}'

	B. Starting with the ID 100000, add 65536 IDs to map for the student user.

		'sudo usermod --add-subuids 100000-165536 --add-subgids 100000-165536 student'
		
	C. Verify the ID ranges.

		'cat /etc/subuid /etc/subgid'
			student:100000:65537
			student:100000:65537
			
1. Start a container that uses the gitea image in a rootless mode.

	A. Attempt to start the gitea container.

		'podman run --rm -p 3030:3030 --name gitea gitea' ->
		Error: OCI runtime error: crun: cannot setresuid to 1001: Invalid argument

	The error occurs because you did not inform the Podman runtime about the new ID ranges.

	B. Migrate the Podman ID ranges.

		'podman system migrate'
		
	C. Start a container that uses the gitea image. Expose port 3030 to access the application.

		'podman run --name gitea --rm -p 3030:3030 gitea'

	D. Go to localhost:3030 in a web browser to verify the application functionality.

	E. In the second terminal, terminate the gitea container.

		'podman stop gitea'

1.	Restore the original user and group IDs from where you placed the backuped files.