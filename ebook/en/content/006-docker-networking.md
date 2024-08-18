# Chapter 6: Docker Networking

Docker networking allows containers to communicate with each other and with the outside world. It's a crucial aspect of Docker that enables the creation of complex, multi-container applications and microservices architectures.

## Docker Network Drivers

Docker uses a pluggable architecture for networking, offering several built-in network drivers:

1. **Bridge**: The default network driver. It's suitable for standalone containers that need to communicate.
2. **Host**: Removes network isolation between the container and the Docker host.
3. **Overlay**: Enables communication between containers across multiple Docker daemon hosts.
4. **MacVLAN**: Assigns a MAC address to a container, making it appear as a physical device on the network.
5. **None**: Disables all networking for a container.
6. **Network plugins**: Allow you to use third-party network drivers.

## Working with Docker Networks

### Listing Networks

To list all networks:

```bash
docker network ls
```

This command shows the network ID, name, driver, and scope for each network.

### Inspecting Networks

To get detailed information about a network:

```bash
docker network inspect <network_name>
```

This provides information such as the network's subnet, gateway, connected containers, and configuration options.

### Creating a Network

To create a new network:

```bash
docker network create --driver <driver> <network_name>
```

Example:

```bash
docker network create --driver bridge my_custom_network
```

You can specify additional options like subnet, gateway, IP range, etc.:

```bash
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 my_custom_network
```

### Connecting Containers to Networks

When running a container, you can specify which network it should connect to:

```bash
docker run --network <network_name> <image>
```

Example:

```bash
docker run --network my_custom_network --name container1 -d nginx
```

You can also connect a running container to a network:

```bash
docker network connect <network_name> <container_name>
```

### Disconnecting Containers from Networks

To disconnect a container from a network:

```bash
docker network disconnect <network_name> <container_name>
```

### Removing Networks

To remove a network:

```bash
docker network rm <network_name>
```

## Deep Dive into Network Drivers

### Bridge Networks

Bridge networks are the most commonly used network type in Docker. They are suitable for containers running on the same Docker daemon host.

Key points about bridge networks:

- Each container connected to a bridge network is allocated a unique IP address.
- Containers on the same bridge network can communicate with each other using IP addresses.
- The default bridge network has some limitations, so it's often better to create custom bridge networks.

Example of creating and using a custom bridge network:

```bash
docker network create my_bridge
docker run --network my_bridge --name container1 -d nginx
docker run --network my_bridge --name container2 -d nginx
```

Now `container1` and `container2` can communicate with each other using their container names as hostnames.

### Host Networks

Host networking adds a container on the host's network stack. This offers the best networking performance but sacrifices network isolation.

Example:

```bash
docker run --network host -d nginx
```

In this case, if the container exposes port 80, it will be accessible on port 80 of the host machine directly.

### Overlay Networks

Overlay networks are used in Docker Swarm mode to enable communication between containers across multiple Docker daemon hosts.

To create an overlay network:

```bash
docker network create --driver overlay my_overlay
```

Then, when creating a service in swarm mode, you can attach it to this network:

```bash
docker service create --network my_overlay --name my_service nginx
```

### MacVLAN Networks

MacVLAN networks allow you to assign a MAC address to a container, making it appear as a physical device on your network.

Example:

```bash
docker network create -d macvlan \
  --subnet=192.168.0.0/24 \
  --gateway=192.168.0.1 \
  -o parent=eth0 my_macvlan_net
```

Then run a container on this network:

```bash
docker run --network my_macvlan_net -d nginx
```

## Network Troubleshooting

1. **Container-to-Container Communication**: 
   Use the `docker exec` command to get into a container and use tools like `ping`, `curl`, or `wget` to test connectivity.

2. **Network Inspection**: 
   Use `docker network inspect` to view detailed information about a network.

3. **Port Mapping**: 
   Use `docker port <container>` to see the port mappings for a container.

4. **DNS Issues**: 
   Check the `/etc/resolv.conf` file inside the container to verify DNS settings.

5. **Network Namespace**: 
   For advanced troubleshooting, you can enter the network namespace of a container:
   ```bash
   pid=$(docker inspect -f '{{.State.Pid}}' <container_name>)
   nsenter -t $pid -n ip addr
   ```

## Best Practices

1. Use custom bridge networks instead of the default bridge network for better isolation and built-in DNS resolution.
2. Use overlay networks for multi-host communication in swarm mode.
3. Use host networking sparingly and only when high performance is required.
4. Be cautious with exposing ports, only expose what's necessary.
5. Use Docker Compose for managing multi-container applications and their networks.

## Advanced Topics

### Network Encryption

For overlay networks, you can enable encryption to secure container-to-container traffic:

```bash
docker network create --opt encrypted --driver overlay my_secure_network
```

### Network Plugins

Docker supports third-party network plugins. Popular options include Weave Net, Calico, and Flannel. These can provide additional features like advanced routing, network policies, and encryption.

### Service Discovery

Docker provides built-in service discovery for containers on the same network. Containers can reach each other using container names as hostnames. In swarm mode, there's also built-in load balancing for services.

## Conclusion

Networking is a critical component of Docker that enables complex, distributed applications. By understanding and effectively using Docker's networking capabilities, you can create secure, efficient, and scalable containerized applications. Always consider your specific use case when choosing network drivers and configurations.
