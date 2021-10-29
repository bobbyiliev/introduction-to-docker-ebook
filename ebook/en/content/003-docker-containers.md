# Working with Docker containers

Once you have your **Ubuntu Droplet** ready, ssh to the server and follow along!

So let's run our first Docker container! To do that you just need to run the following command:

```
docker run hello-world
```

You will get the following output:

![](https://cdn.devdojo.com/posts/images/April2020/docker-run.png)

We just ran a container based on the **hello-world Docker Image**, as we did not have the image locally, docker pulled the image from the **[DockerHub](https://hub.docker.com)** and then used that image to run the container. 
All that happened was: the **container ran**, printed some text on the screen and then exited.

Then to see some information about the running and the stopped containers run:

```
docker ps -a
```

You will see the following information for your **hello-world container** that you just ran:

```
root@docker:~# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
62d360207d08        hello-world         "/hello"            5 minutes ago       Exited (0) 5 minutes ago                       focused_cartwright
```

In order to list the locally available Docker images on your host run the following command:

```
docker images
```


## Pulling an image from Docker Hub

Let's run a more useful container like an **Apache** container for example. 

First, we can pull the image from the docker hub with the **docker pull command**:

```
docker pull webdevops/php-apache
```

You will see the following output:

![](https://cdn.devdojo.com/posts/images/April2020/docker-pull-php-apache.png)

Then we can get the image ID with the docker images command:

```
docker images
```

The output would look like this:

![](https://cdn.devdojo.com/posts/images/April2020/docker-images.png)

> Note, you do not necessarily need to pull the image, this is just for demo purposes. When running the `docker run` command, if the image is not available locally, it will automatically be pulled from Docker Hub.

After that we can use the **docker run** command to spin up a new container:

```
docker run -d -p 80:80 IMAGE_ID
```

Quick rundown of the arguments that I've used:

* `-d`: it specifies that I want to run the container in the background. That way when you close your terminal the container would remain running.

* `-p 80:80`: this means that the traffic from the host on port 80 would be forwarded to the container. That way you could access the Apache instance which is running inside your docker container directly via your browser.

With the docker info command now we can see that we have 1 running container. 

And with the `docker ps` command we could see some useful information about the container like the container ID, when the container was started, etc.:

```
root@docker:~# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                                   NAMES
7dd1d512b50e        fd4f7e58ef4b        "/entrypoint supervi…"   About a minute ago   Up About a minute   443/tcp, 0.0.0.0:80->80/tcp, 9000/tcp   pedantic_murdock
```


## Stopping and restarting a Docker Container

Then you can stop the running container with the docker stop command followed by the container ID:

```
docker stop CONTAINER_ID
```

If you need to, you can start the container again:

```
docker start CONTAINER_ID
```

In order to restart the container you can use the following:

```
docker restart CONTAINER_ID
```


## Accessing a running container

If you need to attach to the container and run some commands inside the container use the `docker exec` command:

```
docker exec -it CONTAINER_ID /bin/bash 
```

That way you will get to a **bash shell** in the container and execute some commands inside the container itself. 

Then, to detach from the interactive shell, press `CTRL+PQ`. That way you will not stop the container but just detach it from the interactive shell.

![](https://cdn.devdojo.com/posts/images/April2020/docker-exec-stop1.png)


## Deleting a container

To delete the container, first make sure that the container is not running and then run:

```
docker rm CONTAINER_ID
```

If you would like to delete the container and the image all together, just run:

```
docker rmi IMAGE_ID
```


With that you now know how to pull Docker images from the **Docker Hub**, run, stop, start and even attach to Docker containers!

We are ready to learn how to work with **Docker images!**
