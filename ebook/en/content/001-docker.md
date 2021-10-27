# Introduction to Docker

It is more likely than not that **Docker** and containers are going to be part of your IT career in one way or another.

After reading this eBook, you will have a good understanding of the following:

* What is Docker
* What are containers
* What are Docker Images
* What is Docker Hub
* How to installing Docker
* How to work with Docker containers
* How to work with Docker images
* What is a Dockerfile
* How to deploy a Dockerized app
* Docker networking
* What is Docker Swarm
* How to deploy and manage a Docker Swarm Cluster

I'll be using **DigitalOcean** for all of the demos, so I would strongly encourage you to create a **DigitalOcean** account follow along. You would learn more by doing!

To make things even better you can use my referral link to get a free $100 credit that you could use to deploy your virtual machines and test the guide yourself on a few **DigitalOcean servers**:

**[DigitalOcean $100 Free Credit](https://m.do.co/c/2a9bba940f39)**

Once you have your account here's how to deploy your first Droplet/server:

[https://www.digitalocean.com/docs/droplets/how-to/create/](https://www.digitalocean.com/docs/droplets/how-to/create/)

I'll be using **Ubuntu 21.04** so I would recommend that you stick to the same so you could follow along.

However you can run Docker on almost any operating system including Linux, Windows, Mac, BSD and etc.


## What is a container?

According to the official definition from the [docker.com](docker.com) website, a container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings.

Container images become containers at runtime and in the case of Docker containers - images become containers when they run on Docker Engine. Available for both Linux and Windows-based applications, containerized software will always run the same, regardless of the infrastructure. Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging.

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/en/assets/infrastructure.png)


## What is a Docker image?

A **Docker Image** is just a template used to build a running Docker Container, similar to the ISO files and Virtual Machines. The containers are essentially the running instance of an image. Images are used to share a containerized applications. Collections of images are stored in registries like [DockerHub](https://hub.docker.com/) or private registries.

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/en/assets/process.png)


## What is Docker Hub?

DockerHub is the default **Docker image registry** where we can store our **Docker images**. You can think of it as GitHub for Git projects.

Here's a link to the Docker Hub:

[https://hub.docker.com](https://hub.docker.com)

You can sign up for a free account. That way you could push your Docker images from your local machine to DockerHub.

