# KUBERNETES (K8S)

## OVERVIEW

Kuberentes is a orchestration service that simplifies the deployment, management, and scaling of containerized
applications. Manages complex pools of resources such as CPU, RAM, storage and networking. Kubernetes
provides a high uptime and fault tolerance for containerized applications and removes the concern that the
developer has regaring how thire application use resources.

The smalles unit in kubernets is a pod which is a single application that consist of one or more containers
including storage resources and an IP address.

--

## KUBERNETES FEATURES

Kubernetes provides a modern container platform with focus on the challenges of running applications at
scale. No matter the size kubernetes has a robust infrastucture and ease of management.

### Horizontal scaling

Appliactions can sale up or down manually or automatic with a configuration set using the terminal or a UI.

### Self-healing

Using user-defined health checks to monitor pods to restart ocr reschedule them in the event of failure

### Automated rollout
 
Kubernetes can gradually roll out updates to an application while checking their status. If something goes
wrong kubernetes can do a roll back to the previous version of the deployment.

### Secret and configuration management

Manage configuration settering and secrets of the applications without the need to rebuild the container.
Applications secrets can be usernames, passwords, service endpoints or any configuration that needs to be
kept private.

### Operators

Operators are packaged Kubernetes applications that also bring the knowledge of the application's lifecycle
into the Kubernetes cluster. Applications that are packaged as operators use the Kubernetes API to update
the cluster's state, and react to changes in the application state.