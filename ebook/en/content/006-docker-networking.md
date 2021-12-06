# Docker Network

Docker comes with a pluggable networking system. There are multiple plugins that you could use by default:

* `bridge`: The default Docker network driver. This is sutable for standalone containers that need to communicate with each other.
* `host`: This driver removes the network isolation between the container and the host. This is suatable for standalone containers which use the host network directly.
* `overlay`: Overlay allows you to connect multiple Docker daemons. This enables you to run Docker swarm services by allowing them to communicate with each other. 
* `none`: Disables all networking.

In order to list the currently available Docker networks you can use the following command:

```
docker network list
```

You would get the following output:

```
NETWORK ID     NAME      DRIVER    SCOPE
3194399146e4   bridge    bridge    local
cf7f50175100   host      host      local
590fb3abc0e1   none      null      local
```

As you can see, we have 3 networks available out of the box already with 3 of the network drivers that we've discussed above.

## Creating a Docker network

To create a new Docker network with the default `bridge` driver you can run the following command:

```
docker network create myNewNetwork
```

The above command would create a new network with the name of `myNewNetwork`.

You can also specify a different driver by adding the `--driver=DRIVER_NAME` flag.

If you want to create a Docker network with a specific range, you can do that by adding the `--subnet=` flag followed by the subnet that you want to use.

## Inspecting a Docker network

In order to get some information for an existing Docker network like the driver that is being used, the subnet, the containers attached to that network, you can use the `docker network inspect` command as follows:

```
docker network inspect myNewNetwork
```

The output of the command would be in JSON by default.

You can use the `docker inspect` command to inspect other Docker objects like containers, images and etc.

## Attaching containers to a network

To practice what you've just learned, let's create two containers and add them to a Docker network so that they could communicate with each other using their container names.

Here is a quick example of a `bridge` network:

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/en/assets/networking.png)

* First start by creating two containers:

```
docker run -d --name web1  -p 8001:80 eboraas/apache-php
docker run -d --name web2  -p 8002:80 eboraas/apache-php
```

> It is very **important** to **explicitly specify a name** with `--name` for your containers otherwise I've noticed that it would not work with the random names that Docker assigns to your containers.

* Once the two containers are up and running, create a new network:

```
docker network create myNetwork
```

* After that connect your containers to the network:

```
docker network connect myNetwork web1
docker network connect myNetwork web2
```

* Check if your containers are part of the new network:

```
docker network inspect myNetwork
```

* Then test the connection:

```
docker exec -ti web1 ping web2
```

Again, keep in mind that it is quite important to explicitly specify names for your containers otherwise this would not work. I figured this out after spending a few hours trying to figure it out.

For more informaiton about the power of the Docker network, make sure to check the official documentation [here](https://docs.docker.com/network/).
