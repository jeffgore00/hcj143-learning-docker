# Learning Docker

# Introduction

Docker partitions a running Linux system into containers. A container is a self-contained sealed unit of software that has everything it needs to run, including the OS (just enough of it to run your code) and networking. 

From the Docker website: "containerized software will always run the same, regardless of the infrastructure. Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging."

Docker differs from virtual machines in that there is only one OS running. 

"[I]t doesn't matter which Linux each container is running on, it just has to be a Linux [system]. And Docker is the program which manages all of this."

So at its core, Docker is a program which builds containers from code. It's almost like Webpack on a larger scale, bundling your code and its dependencies, except the final output is not the "bundle", it's the "container".

Docker is many things besides this:
- A client program, named `docker` - a command you type at the terminal
- A server program that listens for messages from that command and manages a running Linux system.
- A service that distributes containers so people can find each others work
- A company that manages and creates all of the above.


# 1. Installing Docker

## 1-1 Setting up Docker
"Docker's primary job is to manage a Linux server and start and stop your containers on it as required...most of us don't work on laptops running Linux all the time so many people use a virtual machine, running on their laptop to act as the Linux server and run the server side of Docker."

That server side of docker is called the *docker daemon*. And the client side is the docker CLI. The daemon is accessible via a REST API.

From the docs: "The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface."

The docker client is called `docker` and the docker daemon is `dockerd`.

```
docker run hello-world
```

`hello-world` is a container provided by Docker. Output from that command:

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:e7c70bb24b462baa86c102610182e3efcb12a04854e8c582838d92970a09f323
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
```

In the example above, step 2 happened because we didn't have the image locally. What's an image? A "read-only template with instructions for creating a Docker container." And it came from a Docker registry, namely Docker Hub, the main public registry similar to what npm is for Node modules.

## 1-2 Docker Desktop

"boot2docker" is the name a very old Docker CLI.

## 1-3 Install Docker on Mac

It's worth signing up for a Docker ID, it's what you'll use both for "for publishing images to the world and accessing private images."

The "Restart" button in Docker Desktop will restart the Docker VM and fixes most problems. In extreme cases, quit and open again.


# 2. Using Docker

## 2-1 The Docker flow: Images to containers

"An image is every file that makes up just enough of the operating system to do what you need to do."

Images are versioned with their tags. Images don't need a tag or even a name, though (although it may auto-generate one for you if you don't provide one). But they will be assigned an image ID.

The `docker run` command takes an image an turns it into a container with a running process.

```
docker run -ti ubuntu:latest bash
```
Above: Run `bash` within a container created from the latest version of the `ubuntu` image

`-ti`: "It just causes it to have a full terminal within the image so that you can run the shell and get things like tab completion and formatting to work correctly."

To exit this interactive terminal, you can type `exit` or use Ctrl+D.

`docker ps` will give you a list of the running containers.

Containers also have IDs, but these are not the same of the IDs of the images FROM WHICH they were created.

If you don't like the output format of `docker ps`, you can pass it a custom format with the `--format` flag.

Example of how containers are isolated and are instances of images:
1. Run a container from an image
2. In that container (which has its own filesystem), create a file.
3. Run another container from the same exact image in a different terminal
4. Note that the file isn't in the second container.

## 2-2 The Docker flow: containers to images.

A container can be either *running* or *stopped*. The latter happens when the desired process (?) exits. (Can it only run one `COMMAND`?)

By default, `docker ps` does not show stopped containers. You can provide it the `-a` flag to show all containers, or `-l` to show the last container to stop.

Now, let's say that in your container you have some new files that you'd actually like to use to update the image from which future containers are made. You can "commit" a change to the image from the container.

"`docker run` and `docker commit` are complementary to each other. `docker run` takes images to containers and `docker commit` takes containers back to new images."

Note *new* images. It doesn't overwrite the base image.

```
# Create a new image based on the container
docker commit [container ID]
# Result: sha256:23jlk2345jh423lk423jlk32jkl3223
```
Now you have a big long ID (after the :sha256:") of a new image. To give it a meaningful name, just use `docker tag` like so...
```
docker tag 23jlk2345jh423lk423jlk32jkl3223 my-image
```
...or you can just run `docker commit [container ID] [new name]`. It'll still just print the ID of the new image, but it will also have that new name.
