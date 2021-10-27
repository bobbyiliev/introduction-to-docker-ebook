# Installing Docker

Nowadays you can run Docker on Windows, Mac and of course Linux. I will only be going through the Docker installation for Linux as this is my operating system of choice.

I'll deploy an **Ubuntu server on DigitalOcean** so feel free to go ahead and do the same:

[Create a Droplet DigitalOcean](https://docs.digitalocean.com/products/droplets/how-to/create)

Once your server is up and running, SSH to the Droplet and follow along!

If you are not sure how to SSH, you can follow the steps here:

[https://www.digitalocean.com/docs/droplets/how-to/connect-with-ssh/](https://www.digitalocean.com/docs/droplets/how-to/connect-with-ssh/)

The installation is really straight forward, you could just run the following command, it should work on all major **Linux** distros:

```
wget -qO- https://get.docker.com | sh
```

It would do everything that's needed to install **Docker on your Linux machine**.

After that, set up Docker so that you could run it as a non-root user with the following command:

```
sudo usermod -aG docker ${USER}
```

To test **Docker** run the following:

```
docker version
```

To get some more information about your Docker Engine, you can run the following command:

```
docker info
```

With the `docker info` command, we can see how many running containers that we've got and some server information.

The output that you would get from the docker version command should look something like this:

![docker version output](https://imgur.com/tuGSemS.png)

In case you would like to install Docker on your Windows PC or on your Mac, you could visit the official Docker documentation here:

[https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/)

And:

[https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)

That is pretty much it! Now you have Docker running on your machine!

Now we are ready to start working with containers! We will pull a **Docker image** from the **DockerHub**, we will run a container, stop it, destroy it and more!