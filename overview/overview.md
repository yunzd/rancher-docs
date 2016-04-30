## Rancher 概述
---


Rancher 是以在生产环境中运行容器为目标而构建的开源软件平台。随着 Docker 容器这种类型的应用工作负载的逐渐流行，它催生了很多与之相应的基础架构服务，如网络服务、存储服务、负载均衡，安全，服务发现和资源管理。

### 计算资源

Rancher 使用的是来自于公有云或私有云上 Linux 主机的裸计算资源。每一个 Linux 主机既可以是虚拟机，也可以是物理机。Rancher 对每一个主机的期望不会多于 CPU，内存，磁盘存储和网络连接。从 Rancher 的角度看来，一个来自云服务商的云主机和私有数据中心的物理机是没多大差异的。

### 关键功能

Rancher 产品的关键功能包括： 

1. 扩主机网络。 Rancher 为每个环境生成一个软件定义网络，为扩主机和云的容器之间提供了安全的网络通讯。

2. Container load balancing. Rancher provides an integrated, elastic load balancing service to distribute traffic between containers or services. The load balancing service works across multiple clouds.

3. Persistent Storage Services. Rancher supports orchestrating Persistent Storage Services for Docker, making it possible for developers to deploy storage reliably in conjunction with containerized applications. The new feature builds on Docker 1.9 volume plugin capabilities, and makes it easier for developers to run applications that require stateful databases and persistent storage.

4.	Service discovery: Rancher implements a distributed DNS-based service discovery function with integrated health checking that allows containers to automatically register themselves as services, as well as services to dynamically discover each other over the network.

5.	Service upgrades: Rancher makes it easy for users to upgrade existing container services, by allowing service cloning and redirection of service requests.  This makes it possible to ensure services can be validated against their dependencies before live traffic is directed to the newly upgraded services. 

6.	Resource management: Rancher supports Docker Machine, a powerful tool for provisioning hosts directly from cloud providers. Rancher then monitors host resources and manages container deployment.

7. Multi-tenancy & user management: Rancher is designed for multiple users and allows organizations to collaborate throughout the application lifecycle. By connecting with existing directory services, Rancher allows users to create separate development, testing, and production environments and invite their peers to collaboratively manage resources and applications.

8. Multi Orchestration Engines. Rancher supports the ability for users to select the default Cattle, Kubernetes, or Docker Swarm as their container orchestration engine of choice when creating environments.  This will allow users to select market leading scheduling frameworks while still leveraging Rancher features such as the app catalog, enterprise user management, container networking, and storage technologies.

### Primary Consumption Interfaces

There are three primary ways for users to interact with Rancher:

1. Users can interact with Rancher through native Docker CLI or API. Rancher is not another orchestration or management layer that shields users from the native Docker experience. As Docker platform grows over time, a wrapper layer will likely be superseded by native Docker features. Rancher instead works in the background so that users can continue to use native Docker CLI and Docker Compose templates. Rancher uses Docker labels--a Docker 1.6 feature contributed by Rancher Labs--to pass additional information through the native Docker CLI.  Because Rancher supports native Docker CLI and API, third-party tools like Kubernetes work on Rancher automatically.
2. Users can interact with Rancher using a command-line tool called `rancher-compose`. The `rancher-compose` tool enables users to stand up multiple containers and services based on the Docker Compose templates on Rancher infrastructure. The `rancher-compose` tool supports the standard `docker-compose.yml` file format. An optional `rancher-compose.yml` file can be used to extend and overwrite service definitions in `docker-compose.yml`.
3. Users can interact with Rancher using the Rancher UI. Rancher UI is required for one-time configuration tasks such as setting up access control, managing environments, and adding Docker registries. Rancher UI additionally provides a simple and intuitive experience for managing infrastructure and services.

The following figure illustrates Rancher's major features, its ability to run any clouds, and the three primary ways to interact with Rancher.

<img src="{{site.baseurl}}/img/rancher/rancher_overview.png" width="800" alt="Rancher Overview">

### Outline of This Guide

It is easy to get Rancher up and running. If you have access to a Linux VM on your laptop or in a cloud, go to the [Quick Start Guide]({{site.baseurl}}/rancher/quick-start-guide/) to get started right away.

If you are ready to set up a production-grade Rancher installation, follow the instructions in the [Installing Rancher]({{site.baseurl}}/rancher/installing-rancher/installing-server/) to setup a Rancher server and add hosts into the Rancher installation.

Before you start using Rancher, make sure to read through the [Concepts]({{site.baseurl}}/rancher/concepts/) section to understand how Rancher works.

The Configuration section documents how you perform various one-time tasks after you complete installation of Rancher and start using Rancher.

The next three sections--[Using Rancher Through Native Docker CLI]({{site.baseurl}}/rancher/native-docker/), [Rancher Compose]({{site.baseurl}}/rancher/rancher-compose), and [Rancher UI]({{site.baseurl}}/rancher/rancher-ui/applications/stacks/adding-services/)--covers three primary ways you can consume Rancher features.

The [Upgrading Rancher]({{site.baseurl}}/rancher/upgrading) section is essential if you run Rancher in production.

The [Contributing to Rancher]({{site.baseurl}}/rancher/contributing) section contains information on how you can participate in the Rancher open source community.

