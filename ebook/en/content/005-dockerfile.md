# What is a Dockerfile

A **Dockerfile** is basically a text file that contains all of the required commands to build a certain **Docker image**. 

The **Dockerfile** reference page: 

[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

It lists the various commands and format details for Dockerfiles.

## Dockerfile example

Here's a really basic example of how to create a `Dockerfile` and add our source code to an image.

First, I have a simple Hello world `index.html` file in my current directory that I would add to the container with the following content:

```
<h1>Hello World - Bobby Iliev</h1>
```

And I also have a Dockerfile with the following content:

```
FROM webdevops/php-apache-dev
MAINTAINER Bobby I.
COPY . /var/www/html
WORKDIR /var/www/html
EXPOSE 8080
```

Here is a screenshot of my current directory and the content of the files:

![](https://cdn.devdojo.com/posts/images/April2020/dockerfile-example.png)

Here is a quick rundown of the Dockerfile:

* `FROM`: The image that we would use as a ground

* `MAINTAINER`: The person who would be maintaining the image

* `COPY`: Copy some files in the image

* `WORKDIR`: The directory where you want to run your commands on start

* `EXPOSE`: Specify a port that you would like to access the container on

## Docker build

Now in order to build a new image from our `Dockerfile`, we need to use the docker build command. The syntax of the docker build command is the following:

```
docker build [OPTIONS] PATH | URL | -
```

The exact command that we need to run is this one:

```
docker build -f Dockerfile -t your_user_name/php-apache-dev .
```

After the built is complete you can list your images with the docker images command and also run it:

```
docker run -d -p 8080:80 your_user_name/php-apache-dev
```

And again just like we did in the last step, we can go ahead and publish our image:

```
docker login

docker push your-docker-user/name-of-image-here
```

Then you will be able to see your new image in your Docker Hub account (https://hub.docker.com) you can pull from the hub directly:

```
docker pull your-docker-user/name-of-image-here
```

For more information on the docker build make sure to check out the official documentation here:

[https://docs.docker.com/engine/reference/commandline/build/](https://docs.docker.com/engine/reference/commandline/build/)

## Dockerfile Knowledge Check

Once you've read this post, make sure to test your knowledge with this [**Dockerfile quiz**:](https://quizapi.io/predefined-quizzes/basic-dockerfile-quiz)

[https://quizapi.io/predefined-quizzes/basic-dockerfile-quiz](https://quizapi.io/predefined-quizzes/basic-dockerfile-quiz)

This is a really basic example, you could go above and beyond with your Dockerfiles!

Now you know how to write a Dockerfile, how to build a new image from a Dockerfile using the docker build command!

In the next step we will learn how to set up and work with the **Docker Swarm** mode!
