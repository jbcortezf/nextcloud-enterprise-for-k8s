# Nextcloud Enterprise on Kubernetes

Nextcloud is an open-source, self-hosted file sharing and collaboration platform that provides an alternative to public cloud services like Dropbox and Google Drive. It allows you to store, access, and share files from anywhere, and offers features like document editing, task management, and video conferencing.

Kubernetes, on the other hand, is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It provides a powerful and flexible platform for running applications in containers, and offers features like automatic scaling, load balancing, and self-healing.

Running Nextcloud Enterprise on Kubernetes offers many benefits for businesses and organizations. With Kubernetes, you can easily deploy and manage your Nextcloud deployment at scale, while taking advantage of the platform's automatic scaling and load balancing capabilities. This means that you can easily handle spikes in demand, and ensure that your Nextcloud deployment is always available and responsive.

Moreover, Nextcloud Enterprise offers additional features and capabilities that are tailored to the needs of businesses and organizations. It provides enhanced security and compliance features, as well as advanced collaboration and workflow tools, that make it an ideal platform for teams and organizations.

By running Nextcloud Enterprise on Kubernetes, you can take advantage of the power and flexibility of both platforms, while providing a scalable and reliable platform for your business or organization. This repository provides deployment files for Nextcloud Enterprise on Kubernetes, along with documentation and resources to help you get started with your deployment. Whether you are a small team or a large organization, running Nextcloud Enterprise on Kubernetes can provide a secure, flexible, and scalable platform for your file sharing and collaboration needs.

# If you don't have Kubernetes yet, or want to give a try

## Windows
Prerequisites

    Windows 10 Pro, Enterprise, or Education (64-bit)

    Hyper-V and Containers Windows features enabled

    4GB of RAM or higher

    Virtualization support enabled in BIOS

Installation

Download the Windows installer from the Minikube Releases page (https://github.com/kubernetes/minikube/releases/)

    Double-click the installer to run it
    Follow the prompts to complete the installation

Starting Minikube

    Open the Command Prompt or PowerShell as Administrator
    Run the command minikube start
    Wait for the command to finish executing. This may take a few minutes.
    Run the command kubectl cluster-info to verify that Minikube is running

## Linux
Prerequisites

    A Linux distribution that supports systemd (such as Ubuntu 16.04 or later)

    curl installed on the system

    VirtualBox or KVM installed on the system (depending on the driver you choose)

Installation

    Open a terminal window
    Run the command curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    Run the command sudo install minikube-linux-amd64 /usr/local/bin/minikube

Starting Minikube

    Open a terminal window
    Run the command minikube start --vm-driver=<driver> where <driver> is either virtualbox or kvm2
    Wait for the command to finish executing. This may take a few minutes.
    Run the command kubectl cluster-info to verify that Minikube is running

## Mac
Prerequisites

    macOS 10.12 or later

    Virtualization support enabled in BIOS
    
    Homebrew package manager installed on the system

Installation

    Open a terminal window
    Run the command brew install minikube

Starting Minikube

    Open a terminal window
    Run the command minikube start
    Wait for the command to finish executing. This may take a few minutes.
    Run the command kubectl cluster-info to verify that Minikube is running

Note: If you encounter any issues with starting Minikube, please refer to the official Minikube documentation (https://minikube.sigs.k8s.io/docs/start/) for troubleshooting steps.

# Understanding the Components

In this section, we'll take a closer look at the different components that make up our Kubernetes deployment.
## Deployments

In Kubernetes, a Deployment is responsible for managing a set of replicated Pods. It allows you to declaratively manage a set of Pods, and can ensure that a specified number of replicas are running at any given time.

Here are the Deployments we're using in our deployment:

nextcloud: This Deployment manages the Nextcloud application, and ensures that a certain number of replicas are running at any given time.

redis: This Deployment manages the Redis container that is responsible for managing the sessions when scaling Nextcloud.

## Services

In Kubernetes, a Service is an abstraction that defines a logical set of Pods and a policy by which to access them. It provides a stable IP address and DNS name to a set of Pods, and can load-balance traffic between them.

Here are the Services we're using in our deployment:

wserver: This Service is responsible for exposing the Nextcloud application to the outside world. It is a NodePort service, which means that it will listen on a static port on each node in the cluster.

redis: This Service is responsible for exposing the Redis container to the Nextcloud application.

## ConfigMaps

In Kubernetes, a ConfigMap is an object that allows you to store configuration data as key-value pairs. It provides a way to separate configuration from the container image, and can be mounted as a volume in a container.

Here are the ConfigMaps we're using in our deployment:

php-ini-apache2: This ConfigMap contains the configuration file for PHP that is used by the Apache2 server in the Nextcloud container.

php-ini-cli: This ConfigMap contains the configuration file for PHP that is used by the command line interface (CLI) in the Nextcloud container.

## HorizontalPodAutoscalers

In Kubernetes, a HorizontalPodAutoscaler (HPA) is responsible for automatically scaling the number of replicas in a Deployment based on CPU utilization.

Here are the HPAs we're using in our deployment:

redis-autoscaler: This HPA is responsible for automatically scaling the number of Redis replicas based on CPU utilization.

wserver-autoscaler: This HPA is responsible for automatically scaling the number of Nextcloud replicas based on CPU utilization.

That's a high-level overview of the different components that make up our Kubernetes deployment. In the next section, we'll walk through how to deploy this configuration to a Kubernetes cluster.

# Thru the files

## configmap.yaml

This YAML file defines two ConfigMap objects that hold the PHP configuration files for the Nextcloud application. The first one is for the Apache web server and the second one is for the command-line interface (CLI) version of PHP.

Each ConfigMap has a metadata section that defines a unique name for the object. The data section specifies the contents of the configuration file. In this case, the data section contains the configuration settings for PHP, including the session handling configuration.

## deployment.yaml

This YAML file defines a Deployment object for each of the three containers used in the Nextcloud application: the Nextcloud application container, the Redis container, and the MariaDB container.

The metadata section of each Deployment object defines a unique name for the object. The spec section specifies the details of the deployment, including the number of replicas, the image to use for the container, and the ports to expose.

Each Deployment object also specifies a PodTemplate in the spec section. The PodTemplate defines the specification for the pods that will be created by the deployment.

## service.yaml

This YAML file defines three Service objects that expose the Nextcloud application, Redis, and MariaDB containers to the network.

Each Service object has a metadata section that defines a unique name for the object. The spec section specifies the type of service (in this case, a ClusterIP service), the ports to expose, and the selector used to identify the target pods.

The selector field is used to match the service to the pods that it should route traffic to. In this case, the selector matches the labels that were applied to the pods by the corresponding Deployment objects.

## autoscaling.yaml

This YAML file defines two HorizontalPodAutoscaler objects that automatically scale the number of replicas in the Redis and Nextcloud application deployments based on CPU utilization.

The metadata section of each HorizontalPodAutoscaler object defines a unique name for the object. The spec section specifies the scaleTargetRef (the deployment to scale), the minimum and maximum number of replicas, and the metrics to use for scaling.

In this case, the metrics are based on CPU utilization, and the autoscaler will adjust the number of replicas to maintain an average CPU utilization of 50%.

## pvcs.yaml

This YAML file defines a persistent volume claim that is used to store the data for Nextcloud. The accessModes field is set to ReadWriteOnce, which means that the volume can be mounted as read-write by a single node. The resources field sets the requested storage size to 10 gigabytes.

When the nc-deployment.yaml file is deployed, it specifies that a volume should be mounted at the /var/www/html/data directory within the Nextcloud container. This volume is backed by the nc-data PVC that is defined in this file.
