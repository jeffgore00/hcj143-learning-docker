# Learning Docker

Platform: LinkedIn Learning (formerly Lynda)
Instructor: Arthur Ulfeldt
URL: https://www.linkedin.com/learning/learning-docker-2018

# Introduction (with heavy additions from my research)

Docker partitions a running Linux [or Windows] system into containers. A container is a self-contained sealed unit of software that has everything it needs to run, including the OS (just enough of it to run your code) and networking.

From the Docker website: "containerized software will always run the same, regardless of the infrastructure. Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging...Docker is written in the Go programming language and takes advantage of several features of the Linux kernel to deliver its functionality."

Docker differs from virtual machines in that there is only one OS running [either Linux, or Windows]. That OS is "carved up into isolated little spaces."

"[I]t doesn't matter which Linux each container is running on, it just has to be a Linux [system]. And Docker is the program which manages all of this."

> So, let's stop right there. This was correct at one point, but even in 2016 (before this course was released) it became incorrect. Per this link, Docker announced native container support for Windows in Sept. 2016:
> https://devops.com/native-docker-windows-arrives-containers-finally-serverless/
>
> This means that a Docker container's OS can be either Linux _or_ Windows.
>
> Why, then, can Macs like mine run Docker?
>
> That's where Docker Desktop comes in. **Docker Desktop sets up a Linux virtual machine in order to run Linux containers.** So just wanted to hammer this point home - just because Docker is a different concept from a VM, doesn't mean a VM isn't necessary. To run Docker on your Mac, you _need_ a Linux VM, and Docker Desktop handles this for you. (Likewise, Docker Desktop for Windows sets up a Linux VM so that you can run Linux containers.)
>
> - "native macOS virtualization running a custom minimal Linux distro"
>   https://hub.docker.com/editions/community/docker-ce-desktop-mac
> - "Docker.app uses the macOS Hypervisor.framework to run containers."
>   https://docs.docker.com/desktop/faqs/#mac-faqs
>
> So, when I run Docker Desktop on my Mac, what's going on?
>
> ```
>                                                     -> Linux Container 1
> Docker Desktop for Mac -> Linux VM -> Docker Engine -> Linux Container 2
>                                                     -> Linux Container 3, etc
> ```
>
> Notice the "Docker Engine." This is the core functionality of Docker, the client/server model we'll get into shortly. "Docker Engine is the industry’s de facto container runtime that runs on various Linux (CentOS, Debian, Fedora, Oracle Linux, RHEL, SUSE, and Ubuntu) and Windows Server operating systems."
> https://www.docker.com/products/container-runtime
>
> So when I said before that a Docker _container's OS_ could only be Linux or Windows, it's because the Engine can only run on either of those OSs. The Engine is the raw containerization runtime functionality indendepent of virtualization. The engine is what is capable of compartmentalizing the host OS into containers. Therefore Docker Desktop for Mac needs to run a virtualized Linux in order to run the Docker Engine.
>
> On Windows, the story is the same, except for a Windows container, no VM would be necessary. Just partition the host system's OS.
>
> In other words, Docker Desktop is a wrapper around Docker Engine. It provides the engine, but also provides the necessary VM as well as a pleasant user interface.
>
> So, let's make a table.
>
> | Host OS | Container OS | Virtualization           |
> | ------- | ------------ | ------------------------ |
> | Mac     | Linux        | Yes (via Docker Desktop) |
> | Linux   | Linux        | No                       |
> | Windows | Linux        | Yes (via Docker Desktop) |
> | Windows | Windows      | No                       |
>
> \*\*Side side note: a Mac cannot run Windows containers. Only Windows machines can run Windows containers.
> https://stackoverflow.com/questions/54650721/can-i-run-windows-containers-on-docker-desktop-for-mac
>
> (Now, technically, you could have your Mac run a virtual Windows machine which in turn runs a Windows container: "Docker Desktop can run inside a Windows 10 VM running on apps like Parallels or VMware Fusion on a Mac provided that the VM is properly configured. However, problems and intermittent failures may still occur due to the way these apps virtualize the hardware. For these reasons, Docker Desktop is not supported in nested virtualization scenarios.")
> https://docs.docker.com/docker-for-windows/troubleshoot/#running-docker-desktop-for-windows-in-nested-virtualization-scenarios
>
> \*\*\*Side side side note: It doesn't look like there are any plans to be able to run native Mac OS containers. I Googled and couldn't find anything. Perhaps Linux containers are so similar already (due to Unix background), that there isn't much need for it...

So at its core, Docker is a program which builds containers from code. It's almost like Webpack on a larger scale, bundling your code and its dependencies, except the final output is not the "bundle", it's the "container".

Docker is many things:

- A client program, named `docker` - a command you type at the terminal
- A server program that listens for messages from that command and manages a running Linux or Windows system.
- A service that distributes containers so people can find each others work
- A company that manages and creates all of the above.

These first two bullets, the client-server program, comprises the "Docker Engine" of containerization. Per Docker:

> Docker Engine acts as a client-server application with:
>
> - A server with a long-running daemon process `dockerd`.
> - APIs which specify interfaces that programs can use to talk to and instruct the Docker daemon.
> - A command line interface (CLI) client `docker`.

# 1. Installing Docker

## 1-1 Setting up Docker

"Docker's primary job is to manage a Linux server and start and stop your containers on it as required...most of us don't work on laptops running Linux all the time so many people use a virtual machine, running on their laptop to act as the Linux server and run the server side of Docker."

That server side of docker is called the _docker daemon_. And the client side is the docker CLI. The daemon is accessible via a REST API.

> "A daemon is a process that runs in the background rather than under the direct control of the user."

From the docs: "The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface."

The docker client is called `docker` and the docker daemon is `dockerd`.

```bash
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

(Note that at the time I took those notes, I was on my work machine. Now on my new personal Mac M1, `(amd64)` is now `(arm64v8)`)

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

```bash
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

A container can be either _running_ or _stopped_. The latter happens when the desired process (?) exits. (Can it only run one `COMMAND`?)

By default, `docker ps` does not show stopped containers. You can provide it the `-a` flag to show all containers, or `-l` to show the last container to stop.

Now, let's say that in your container you have some new files that you'd actually like to use to update the image from which future containers are made. You can "commit" a change to the image from the container.

"`docker run` and `docker commit` are complementary to each other. `docker run` takes images to containers and `docker commit` takes containers back to new images."

Note _new_ images. It doesn't overwrite the base image.

```bash
# Create a new image based on the container
docker commit [container ID]
# Result: sha256:23jlk2345jh423lk423jlk32jkl3223
```

Now you have a big long ID (after the :sha256:") of a new image. To give it a meaningful name, just use `docker tag` like so...

```bash
docker tag 23jlk2345jh423lk423jlk32jkl3223 my-image
```

...or you can just run `docker commit [container ID] [new name]`. It'll still just print the ID of the new image, but it will also have that new name.

"All Docker containers have names; if you choose not to name your container, Docker will name the container for you." See the `--name` flag later on.

- `docker images` will list all the images on your machine.

## 2-3 Run processes in containers

"The `docker run` command converts an image to a living, running container, with a process in it that is doing something."

`docker run` starts a container by giving an image name and a process to run in that container. That process is the container's "main process", and when that main process stops, the container stops. (note that means that other processes can be started and stopped without causing the container to stop)

`docker run -rm` is a common command; `-rm` will remove the container (rather than leaving it stopped) when its main process stops.

This container is created from the ubuntu image. The main process is bash, and we give bash the command `-c` to execute. Sleep, then (`;`) print "all done"

```bash
docker run -ti ubuntu bash -c "sleep 3; echo all done"
```

`docker run -d` means run a container in the background ("detached" / `--detach`). It will just print the container ID.

`docker attach [container name or container ID]` will let you jump into the terminal of that attached container (naturally, the container must be running and not stopped). Technically, it "attach[es] [the container] to STDIN, STDOUT or STDERR".

Ctrl + P followed by Ctrl + Q allows you to re-detach that container.

`docker exec` "runs a new command in a running container. The command started using docker exec only runs while the container’s primary process (PID 1) is running, and it is not restarted if the container is restarted."

For instance

```bash
docker exec -ti soft_baby bash
```

Will start a new `bash` process in the already running "soft_baby" container. This is good for debugging a container in which the main process is malfunctioning.

## 2-4 Manage containers

Docker keeps the output of the container around as long as the container exists. `docker logs [container name or id]` allows you to see the output. In the case of the `bash` process running as the primary container process, it allows you to see all the commands executed and their outputs, like a read-only shell.

Supposedly, too much output within a container will slow it down.

How to stop and remove a container?

`docker kill` - stops a container
`docker rm` - removes a stopped container. You cannot remove a running container.

(And likewise, `docker start` will start a stopped container.)

`docker ps -l` gives you the latest created (`-l`) container.

You can give your containers both memory and CPU limits. These are `--memory`, `--cpu-shares`, and `--cpu-quota` flags that are supplied to `docker run`.

CPU shares is relative to other containers, CPU quota is a hard/absolute limit.

Most orchestration systems will require you to state the memory/CPU limits of a container.

Author says: if you're running a Node app in a container which has dependencies, such as from npm, do not fetch the dependencies when the container starts. It is possible that someone could remove one of the dependencies from npm. Just include the dependencies _with_ the container (I guess with an image that includes them).

JG Note: I doubt this is that big of a risk. Packages are versioned and seems difficult to unpublish them, unless it's really a fringe package.

But a better reason, it would also make the containerized Node app to take possibly much longer to get up and running, since it has to install its dependencies. Better to have a bigger container that's ready to go, than a leaner one that's slow. :)

So, seems like...

GOOD:
Image contains `npm ci` command -> creates container with dependencies preloaded

BAD:
Image contains only the app -> creates container -> container executes `npm ci` on start

## 2-5 Exposing ports (connecting container to host)

Programs in containers are isolated from the internet by default.

Side note. Let's learn about `netcat` i.e. the `nc` Linux command. This is used in the Docker example of communicating with containers.:

> https://linux.die.net/man/1/nc
>
> It is quite simple to build a very basic client/server model using nc. On one console, start nc listening on a specific port for a connection. For example:
>
> $ nc -l 1234
> nc is now listening on port 1234 for a connection. On a second console (or a second machine), connect to the machine and port being listened on:
>
> $ nc localhost 1234
> There should now be a connection between the ports. Anything typed at the second console will be concatenated to the first, and vice-versa. After the connection has been set up, nc does not really care which side is being used as a 'server' and which side is being used as a 'client'. The connection may be terminated using an EOF

So, how do you communicate with a docker container via network ports?

Well first, you may find that `nc` is not available within your `ubuntu:latest` container. You could do one of two things:

1. Install it within the container:
   ```bash
   apt-get update && apt-get install -y netcat
   ```
2. Use an older Ubuntu that bundles `nc` preinstalled. 14.04 is the version the author uses here.

OK, so, how do you communicate with a docker container via network ports? You supply the `-p` flag to `docker run`. You'd think that means "port", but it actually means `--publish`, to "publish a container's port(s) to the host."

So, the container has its own ports, which makes sense, since the container is a running OS in its own right. And then your own computer, of course, has ports. So you have to map the published container port to a port on your own machine.

Here's an example. This command binds port `1234` of the container to port `1234` of the host machine.

```bash
docker run -p 1234:1234 -ti ubuntu bash
```

You can then test the connection by running `nc -l 1234` within the container and `nc localhost 1234` in another terminal on your machine.

What about vice versa? What if you wanted to start listening on the local machine and then connect to it from the container?

You _could_ just provide the IP address of the host. But more reliable is to use a DNS name, `host.docker.internal`, that will always get you the correct IP address of the host.

"[If you] want to connect from a container to a service on the host....The host has a changing IP address (or none if you have no network access). We recommend that you connect to the special DNS name host.docker.internal which resolves to the internal IP address used by the host."

So, with that said:

```bash
# Shell 1. Host machine (your computer)
nc -l 1234

# Shell 2. Docker cotainer
nc host.docker.internal 1234
```

Huge tip: if you want the container to be removed after it stops, use `docker run` with the `--rm` flag. Note two dashes!

Also note that the `-p` flag needs to come _before_ specifying the image. Otherwise the published flag is treated like part of the image name.

```bash
$ docker run -ti ubuntu bash -p 1234:1234
bash: 1234:1234: No such file or directory
$ docker run -p 1234:1234 -ti ubuntu bash
# bash: cool, nice job
```

What if you want Docker to _choose_ the port on your host? For instance, you may just want it to pick whatever unused port is available. This is good for many containers running simultaneously with their own fixed ports internally. For instance, let's say there are two containers that both run on port 3000 internally. They cannot both bind to the single 3000 port on your machine.

Note only the left side of the port assignment is supplied here - the internal port.

```
docker run --rm -p 1234 -ti ubuntu bash
```

How do we find out what host port it selected? Well, lets say this running container's name is `jeff_rules`. We can then do:

```bash
$ docker port 0a4c1dc64faa
1234/tcp -> 0.0.0.0:52546
```

^ The above shows that port 1234 of the container has a TCP connection to port 52546 of the local host machine.

By default, the `-p` maps TCP ports. If you want to use UDP instead, you could add `/udp` to the end, like so

```bash
# Map UDP port 80 in the container to port 8080 on the Docker host.
-p 8080:80/udp
```

Generally, the protocol can be supplied at the end like the above.

(Likewise, `nc -l` defaults to listening on a TCP port. To listen on a UDP port, you'd do `nc -ul`. Supposedly, TCP is more reliable.)

## 2-6 Connecting Directly Between Containers

Given what we've learned above, we _could_ infer that communicating between containers could be done via the host machine.

```
container A sends message to host on port 1 ->  host sends message to container B via port 2
```

But, there's a better way. First, lets learn about `docker network` command, which "lists all the networks the Engine daemon knows about."

```
$ docker network ls

NETWORK ID     NAME      DRIVER    SCOPE
4e965815f5f0   bridge    bridge    local
3828717c6685   host      host      local
54c639277474   none      null      local
```

These three networks are baseline instances of the DRIVER. So let's focus on the driver values:

- `host` = container shares the host's networking namespace. I.e. the container's port 8080 _is_ the host's port 8080. Therefore the `-p` port mapping doesn't work in this mode, because the ports are already mapped. Note that this only works on Linux hosts; isn't supported in Docker Desktop for Mac or Windows (perhaps because some ports are already reserved there.)

- `null` = no driver, networking for this container is disabled entirely.

- `bridge` = none of the above, the default network driver. "In terms of Docker, a bridge network uses a software bridge which allows containers connected to the same bridge network to communicate...Bridge networks apply to containers running on the **same** Docker daemon host."

In other words, containers can communicate with each other via the `bridge` inter-container network driver rather than using a host machine which is explicitly listening as their go-between.

```bash
$ docker network create learningNetwork
```

By default, this creates a new network named "learningNetwork" with the `bridge` driver.

Let's create a new Ubuntu 14.04 container called `containerA` running within that network:

```bash
$ docker run --rm -ti --net learningNetwork --name containerA ubuntu:14.04 bash
```

We can have the container ping itself, just to show an IP address exists:

```bash
root@containerA ping containerA
```

Now we'll create a new Ubuntu 14.04 container called `containerB` running within that same network:

```bash
$ docker run --rm -ti --net learningNetwork --name containerB ubuntu:14.04 bash
```

And now let's ping container A from container B:

```bash
root@containerB ping containerA

PING containerA (172.18.0.2) 56(84) bytes of data.
64 bytes from containerA.learningNetwork (172.18.0.2): icmp_seq=1 ttl=64 time=0.155 ms
```

We could do vice versa, too, pinging container B from container A:

```bash
root@containerB ping containerA

PING containerB (172.18.0.3) 56(84) bytes of data.
64 bytes from containerB.learningNetwork (172.18.0.3): icmp_seq=1 ttl=64 time=0.136 ms
```

Note that `172` is one of the reserved blocks for "private internet".
https://datatracker.ietf.org/doc/html/rfc1918

The bridge network IP address of the gateway between the docker host is always `172.X.0.1`.

X is variable depending on the the network. The default `bridge` network is `172.17.0.1`. Assuming there are no other networks and create a new `bridge` driver network, that increments by 1, to `172.18.0.1`.

Then, when you add containers to that network, they increment that final version. In the `learningNetwork` network:

Gateway: `172.18.0.1`
containerA: `172.18.0.2`
containerB: `172.18.0.3`

Note also that you could use `ping containerA` rather than `ping 172.18.0.2`. That's because Docker, in a _user-defined network_ like our own brand new `learningNetwork` network, automatically maps the container name to a DNS name in the associated network.

To connect an existing container to an existing network, you can use `docker network connect`.

```bash
docker network connect containerA learningNetwork
```

A container can be connected to more than one Docker network.

## 2-7 Legacy linking

Side note: you can start a container with given env vars using the `-e` flag.

Another side note: the LS_COLORS environment variable is responsible for the colors that you see when you run the `ls` command.

"How does legacy linking work? by connecting ports on one container to all the ports on the other, but not in the other direction."

I skipped this video after halfway through. It was alraedy "legacy" when this course was last updated nearly 2 years ago. And Docker page says this:

> The --link flag is a legacy feature of Docker. It may eventually be removed. Unless you absolutely need to continue using it, we recommend that you use user-defined networks to facilitate communication between two containers instead of using --link. One feature that user-defined networks do not support that you can do with --link is sharing environment variables between containers. However, you can use other mechanisms such as volumes to share environment variables between containers in a more controlled way.

## 2-8 Listing Images

`docker images` lists the images you've downloaded.

When you `docker commit` without a tag, it will automatically assign the value `latest` as the tag:

```bash
# Using the container 5be787b68ac6, create a new image called core-image with the tag "latest
docker commit 5be787b68ac6 core-image
```

To add a tag, just add a `:[tag]` after the new image name, i.e.

```bash
# Using the container 5be787b68ac6, create a new image called core-image with the tag "v2.1"
docker commit 5be787b68ac6 core-image:v2.1
```

`docker pull` downloads the specified image. It's run automatically by `docker run` (when specificied image is not existing locally, download it with `pull`, then `run` it). But it's useful if you know you're going to be offline and you want to download it ahead of time.

You rememember `docker rm`? That's for containers. To remove an image, you want `docker rmi`.

```
docker rmi [name]:[tag] or
docker rmi [image-id]
```

As with URLs and filepaths, with Docker images you can supply limited details (relative/implied) or all the details (absolute/expclit). Per Docker, "an image name is made up of slash-separated name components, optionally prefixed by a registry hostname."

Here is an example of a "fully-qualified" image name:

```
mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Let's dig a bit more into the structure of this name. It can be boiled down to:

```
<registry[:port]>/<repository[:version-tag]>
```

First of all, note: where is the image name? The repository _is_ the image name. Just like a github repository, which only refers to one codebase, so too does the image repository refer to only one image, but it can have many versions.

```bash
# Spaced out for clarity
<registry[:port]>  /<repository      :version-tag   >
mcr.microsoft.com  /oss/nginx/nginx  :1.15.5-alpine

# Proof that /oss/nginx/nginx is the repo name:
# https://mcr.microsoft.com/v2/_catalog
```

Note that the `repository` field can support subdirectories. Check this excellent article which breaks down Docker qualified image names:
https://stevelasker.blog/2020/02/17/registry-namespace-repo-names/

"Different registry operators support different levels of depth. Docker Hub supports two levels: `org/repo`. Azure Container Registry supports as many as you wish, up to the Docker client's max length of 256 characters."

So, with that said, Docker Hub images is very similar to npm in the namespacing aspect of a repository. Just like npm has `scope` which allows for packages like `@wdio/cli` (i.e. `<scope/repo>`), so Docker has the concept of scope. This allows users to publish images with the same name. In fact, scope is a compulsory aspect of the image name in Docker Hub, since the top-level scope is controlled by Docker themselves (unlike npm, in which anyone can publish an unscoped package.)

So, let's break down a Docker Hub image name.

```bash
# Again, the same format from above, a generic format that works for all registries
<registry[:port]>/<repository[:version-tag]>

# Really, the "repository" in Docker Hub is the organization (aka username) + the image name.
# And registry is unneeded since it's Docker Hub by default.
<organization/image-name[:version-tag]>

# Spaced out for clarity
/<organization/image-name          :version-tag>
/vvoyer       /selenium-standalone :latest
```

Therefore if I were to publish an image named `robot-builder`, the image name / repo name would be `jgore/robot-builder`. Yet if Docker, Inc. did it themselves, it would just be called `robot-builder`. This is "the top-level namespace for [official images](https://docs.docker.com/docker-hub/official_images/)", which are "a curated set of Docker repositories hosted on Docker Hub."

> Note: Docker's public registry is located at `registry-1.docker.io`.

## 2-9 Volumes

Volumes allow you to share data between containers or between a container and the host. The are virtual "discs".

There are two types:

- Named (Persistent) - stays on host even after container is gone
- Anonymous (Ephemeral) - only exists when container is running

From the [Docker docs](https://docs.docker.com/storage/volumes/):

> A Docker data volume persists after a container is deleted. There are two types of volumes to consider:
>
> - Named volumes have a specific source from outside the container, for example awesome:/bar.
> - Anonymous volumes have no specific source so when the container is deleted, instruct the Docker Engine daemon to remove them.

Volumes are _not_ part of images. They're for your local data.

1. How to create a named volume (for sharing between host <-> container(s)).

Let's say, for example, that I want to share my host Downloads folder `/Users/jeffgore/Downloads` with a container, and within that container, the folder would be called `/downloads_from_host`.

(Note: On Mac, you will be prompted to allow Docker to have access to your Downloads directory.)

The magic flag here, passed to `docker run`, is the `-v` volumes flag. It is little more than a mapping between directories and/or files. The mapped file/directory within the container is created within the container when it starts.

Here's how you would pass the arguments to the `-v` flag:

```bash
-v <absolute-path-in-host-machine>:<absolute-path-in-container>
-v /Users/jeffgore/Downloads:/downloads_from_host

# Full command to run Bash in the latest Ubuntu with this volume
docker run --rm -ti -v /Users/jeffgore/Downloads:/downloads_from_host ubuntu bash
```

Important note for the host machine file:

- If it is a directory and doesn't exist, Docker will create a new directory on your host machine
- If it is a file and doesn't exist, Docker will create a new _directory_ on your host machine with that filename as its name

Since the Docker container and the host machine are pointing towards the same files, then you can modify the files from either the container or your host machine.

2. How to create an anonymous volume (for sharing between containers)

The first step is pretty similar, except there is no `:` supplied to the `-v` flag. This signals that the directory should be created in the container only, and will be sharable with other containers. Let's call this shared directory `container_collab` within the first container, `containerA`.

```bash
docker run --rm -ti -v /container_collab --name containerA ubuntu bash
```

Now that one container has been initialized with a sharable anonymous volume, subsequent containers can be started that point to that volume. You do this with the `--volumes-from` flag which takes an existing container as an argument. Let's start another container, `containerB`, with that argument instructing it to mount to its filesystem all the volumes from `containerA`.

```bash
docker run --rm -ti --volumes-from containerA --name containerB ubuntu bash
```

What's interesting is that there is nothing particularly special about the initializing container. If `containerA` is stopped, then the shared volume still exists, and a new container could point at `containerB` as the new source in `--volumes-from`.

The volumes only disappear when all containers pointing to it are stopped are removed (note you can stop the containers and the shared volume will still be there).

## 2-10 Docker Registries

Images are hosted in registries. Registries manage and distribute images.

`docker search` will search the Docker Hub registry.

```
docker search webdriver
```

To push and pull images from Docker Hub, you have to `docker login` first.

# 3. Building Docker images

## 3-1. What are Dockerfiles?

Dockerfiles are the way you "build images with code". The are instructions on how to create an image. The command that reads those instructions is `docker build`.

This command says "Build an image with the name "puppiesImages" and the tag "v1.0" per the instructions in the `Dockerfile` located in the `/puppies` directory:

```bash
docker build -t puppiesImage:v1.0 ./puppies
```

Likewise, the current directory would just be `.`.

When the command finishes, the result is published in your local docker registry.

"Each line in a Dockerfile is its own call to `docker run`."

Processes you start on one line will not be running on the next line. Therefore if you need two processes to be executing simultaneously, they need to be initialized on the same line.

Environment variables will be available to subsequent lines, though.

## 3-2 Buliding Dockerfiles

A Dockerfile must begin with a `FROM` instruction. `FROM` specifies the "parent image" to use as a base.

Next, you can add `RUN` instructions. "The RUN instruction will execute any commands in a new layer on top of the current image and commit the results."

Note "commit the results". Think of your image as a repo. In fact, that's what the image is actually called. The RUN commands are also your commits.

Finally, after you've set up your image to your liking with all your `RUN` commands, you can list your `CMD`. This is the default command for a container if a `COMMAND` is not supplied to `docker run`. "The main purpose of a CMD is to provide [a default] for an executing container...There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect."

"Do not confuse RUN with CMD. RUN actually runs a command and commits the result; CMD does not execute anything at build time, but specifies the intended command for the image."

So with all that said, let's containerize a small Node-equipped system. (Note: The `node` image is based on the `buildpack-deps` image which is based on the `debian` image.)

In this example, the container will be equipped with two Node scripts: `countdowntimer.js`, which counts down by 1 from 100000, and `countuptimer.js`, which counts up infinitely by 1 from 1.

If the container is started with no `COMMAND`, then `countuptimer.js` will be run by default

```dockerfile
# This is a comment. Dockerfiles allow comments. :)
FROM node:14.16.1
RUN mkdir timers
RUN cd timers
RUN echo "let i = 100000; setInterval(() => { console.log(i--) }, 1000);" > countdowntimer.js
RUN echo "let i = 1; setInterval(() => { console.log(i++) }, 1000);" > countuptimer.js
CMD node countuptimer.js
```

Let's build this into an image called `jeff-node`, with tag `v1`:

```bash
docker build -t jeff-node:v1 .
```

And finally let's execute it:

```bash
# First, execute with no command arg. This means the Dockerfile CMD will be run.
$ docker run --rm -ti --name nodeTestContainer jeff-node:v1
1
2
3
4
# (exit)
# Now, lets execute with a command argument. We know the countdowntimer.js file exists in the container, so...
$ docker run --rm -ti --name nodeTestContainer jeff-node:v1 node countdowntimer.js
100000
99999
99998
99997
# (exit)
```

So, with that said, we see how the `docker build` process works.

1. Use a base image to build a temporary container
2. Make changes to that temporary container
3. Use that container as the basis for a new image

Let's break down step 2 a bit more. There are actually substeps within that step. Every line of instruction in a `Dockerfile`, when executed, creates an "intermediate container". "Each line of a Dockerfile makes a new, independent image based on the previous line's image."

https://stackoverflow.com/questions/39705085/how-are-intermediate-containers-formed

> When you build a new image, Docker does this for each instruction (RUN, COPY etc.) in your Dockerfile:

> 1. create a temporary container from the previous image layer (or the base FROM image for the first command;
> 2. run the Dockerfile instruction in the temporary "intermediate" container;
> 3. save the temporary container as a new image layer.

> The final image layer is tagged with whatever you name the image.

If you want to see proof of this, run `docker history <imagename:tagname>` and see the resulting table:

```bash
docker history jeff-node:v3
```

Output:

```
IMAGE          CREATED         CREATED BY                                      SIZE
aaa884050cfe   7 minutes ago   CMD ["/bin/sh" "-c" "node countuptimer.js"]     0B
<missing>      7 minutes ago   RUN /bin/sh -c echo "let i = 1; setInterval(…   58B
<missing>      7 minutes ago   RUN /bin/sh -c echo "let i = 100000; setInte…   63B
<missing>      7 minutes ago   RUN /bin/sh -c cd timers # buildkit             0B
<missing>      7 minutes ago   RUN /bin/sh -c mkdir timers # buildkit          0B
```

The images for the intermediate containers are "`<missing>`" because by default, intermediate containers are deleted after `docker build` completes. You can change this with a flag, if you want.

Note that `docker build` will cache data and look to the cache on subsequent runs.

Side note: "BusyBox is a software suite that provides several Unix utilities in a single executable file." BusyBox has a Docker image.

Side note: `apt-get` is the Linux package install command. `apt-get -y update` means automatically say "Yes" to prompts, and `update` means to "re-synchronize the package index files from their sources".

This was used in the example, where `nano` is the name of the package to be installed. (It's a CLI text editor.)

```
apt-get -y update
apt-get install nano
```

To add an instruction to copy a file from your host machine to the Docker container in the image creation, you would use the `ADD` command.

```
ADD <host file relative path> <container file absolute path>
```

Let's rephrase our previous example to use JavaScript files sourced from our local machine, rather than writing the code inline.

```dockerfile
FROM node:14.16.1
RUN mkdir timers
ADD countdowntimer.js /timers/countdowntimer.js
ADD countuptimer.js /timers/countuptimer.js
CMD node timers/countuptimer.js
```

This is looking better. But there's still at least one more thing we could improve. Note these two lines, which run an executable with an argument.

```dockerfile
CMD node timers/countuptimer.js
```

Note we are specifying this command _as if it were running in a shell_. Docker recognizes this and calls this "shell form". When it sees RUN or CMD in shell form, runs the command as an input to a shell process, which is `/bin/sh -c` on Linux.

So `CMD node timers/countuptimer.js` in the Dockerfile translates into `/bin/sh -c "node timers/countuptimer.js"` in the container.

Note there are a couple potential problems with doing this.

1.  Not all images have the a shell executable. Remember, Docker images have just enough of what they need to function. Some images do not need to be built on top of an OS like Debian. If you were to try to `RUN` with a shell format based on the `hello-world` image, then it would fail, because that image doesn't even have a shell installed.
2.  The shell may not receive the argument as you intended due to "string munging". Still not sure an example of this, but probably applies to arguments with spaces or special chars.
3.  The shell is an extra unnecessary process to run an executable.

Therefore we can use the "exec form" of `CMD` instead. This is the recommended form:

```dockerfile
FROM node:14.16.1
RUN mkdir timers
ADD countdowntimer.js /timers/countdowntimer.js
ADD countuptimer.js /timers/countuptimer.js
CMD ["node", "timers/countuptimer.js"]
```

Note that `RUN` also has an "exec" form. So why didn't we use it? Because `mkdir` is a command of the shell, not an independently executable binary in `/bin`.

Still, if we wanted, we could have done this:

```dockerfile
RUN [ "sh", "-c", "mkdir timers" ]
```

Moving along...setting environment variables is very simple/easy. Note that not only will those env variables be available for the rest of the `docker build` process, but also in the running container process as well.

```dockerfile
ENV COUNT_DOWN_TO=1
ENV COUNT_UP_TO=100000
```

Now, let's say you wanted to access/print the value of an environment variable, i.e. `$HOME`. In this case, you would need the shell to do the variable substitution. That is part of normal "shell processing".

## 3-3 Dockerfile syntax

LABEL is like the HTML <meta> field - a place for metadata. It is provided in `key="value"` format. For example:

```dockerfile
LABEL author="Jeff Gore <jgore00@gmail.com>"
```

Note that `ADD` will automatically unpack `tar` files:

```dockerfile
# This will unpack the host TAR file into `/project` in the container
ADD project.tar.gz /project
```

It will also automatically download the contents of a URL argument:

```dockerfile
ADD https://jeffgore.info/headshot.jpg /headshots
```

`ENTRYPOINT` is a keyword that lets you establish _the first part of a command_ in order to "configure a container that will run as an executable".

For instance, say you want your container to be able to accept a node script as an argument. Node that last time we had to do this. Note we still have to tell it to use `node` to execute the file.

```
docker run --rm -ti --name nodeTestContainer jeff-node:v1 node countdowntimer.js
```

How would we be able to do this instead?

```
docker run --rm -ti --name nodeTestContainer jeff-node:v1 countdowntimer.js
```

With the `ENTRYPOINT`, of course. The entry point is the "base" of any command. If you supply one, all commands will be prepended with it, whether that's the default `CMD` command, or one explicitly passed to the container.

```dockerfile
FROM node:14.16.1
LABEL author="Jeff Gore <jgore00@gmail.com>"
RUN mkdir timers
ADD countdowntimer.js /timers/countdowntimer.js
ADD countuptimer.js /timers/countuptimer.js
ENV COUNT_DOWN_TO=1
ENV COUNT_UP_TO=100000
# note that if there were more args needed, you would have to pass them as inidividual array members
ENTRYPOINT ["node"]
CMD ["timers/countuptimer.js"]
```

Where the `ENTRYPOINT` is very useful is if you wanted to distribute a command line tool.

Now...remember the `-p` flag we used to map ports? `EXPOSE` is a keyword that _does not do that for us_, but is useful in this way: "The `EXPOSE` instruction does not actually publish the port. It functions as a type of documentation between the person who builds the image and the person who runs the container, about which ports are intended to be published."

To actually publish the port, you still need to use the `-p` flag with `docker run`.

Now, onto volumes. `VOLUME` is supported but behaves differently from the `-v` flag. With the flag, you can supply one or two arguments:

```
-v <directory to share with container(s)>
-v <host machine directory> <directory to share with host and container(s)>
```

In a Dockerfile, however, "you can’t mount a host directory from within the Dockerfile...The `VOLUME` instruction does not support specifying a host-dir parameter...This is to preserve image portability, since a given host directory can’t be guaranteed to be available on all hosts."

Therefore if you supply more than one argument to `VOLUME`, it signifies just extra directories to share within the container. (Note that if the directory doesn't exist, `VOLUME` will just create it.)

This example creates two directories and makes them volumes.

```dockerfile
FROM ubuntu
VOLUME ["/shared-dir-1","/shared-dir-2" ]
```

Note that if you are extending an OS container like `ubuntu`, the default working directory is root. If you want the default directory to be different, you can use the `WORKDIR` command.

```dockerfile
FROM ubuntu
VOLUME ["/shared-dir-1","/shared-dir-2" ]
WORKDIR "/shared-dir-1"
# From this point forward, the current directory for build steps is shared-dir-1, and when the container starts, it will be with that working directory.
```

The `USER` command lets you start the container as a specific user.

"By default, containers run as root. A container running as root has full control of the host system...The default user in a Dockerfile is the user of the parent image."

## 3-4 Multistage builds

Let's take this example simple CLI tool:

```dockerfile
# url-builder v1.10
FROM openjdk
ADD https://github.com/jeffgore00/url_builder/raw/master/dist/url-builder-v1.jar /url-builder.jar
ENTRYPOINT [ "java", "-jar", "url-builder.jar" ]
```

It depends on a Java environment to run a .jar file which has previously been compiled from Kotlin and published to source control.

What if, however, you didn't want to have to manually build and publish a new version of your code and then change your Dockerfile every time you pushed a change? What if you didn't want to commit your build artifact to source control?

_What if you wanted Docker to compile the code as part of building the image?_

This makes sense, especially if you want to publish a new docker image as a part of your standard release process.

Well, easy, you say. Just find a Docker image that has both the Java runtime and the Kotlin compiler, and then `RUN` the compilation before executing it.

```dockerfile
# Doesn't exist, but easy to imagine
FROM imageThatHasBothKotlinCompilerAndJavaRuntime

# Presumably, this Dockerfile is in the same dir as the main.kt Kotlin source code. So, copy that into the container.
ADD main.kt ./main.kt

# Compile the code into a .jar file. Note "kotlinc" is misspelled on purpose because a VS Code extension for Kotlin is making the syntax highlighting go wild.
RUN cotlinc main.kt -include-runtime -d url-builder.jar

ENTRYPOINT [ "java", "-jar", "url-builder.jar" ]
```

That would work, but there's one problem. Note that the base image needs to have both the Java runtime and the Kotlin compiler, which means that it will likely be fairly large. And it's a waste, because by the time the image is actually ready for consumption by somebody else, it doesn't need the Kotlin compiler - the compiled file is already in the image!

Enter _multistage builds._

Multistage builds allow you to use multiple `FROM` statements to utilize different base images as part of the build process. The built image will only be based on the final `FROM` statement.

These base images can be labeled with `as`, and then files transfered between the layers in different `FROM` blocks.

Let's refactor our above example to use a multistage build.

```dockerfile
# Note that this Kotlin image currently doesn't work for Apple Silicon, so I haven't actually been able to test this.
FROM zenika/kotlin as builder
ADD main.kt ./main.kt
RUN ["cotlinc", "main.kt", "-include-runtime", "-d", "url-builder.jar"]

FROM openjdk
# Note here how we copy a file from one stage to another
COPY --from=builder url-builder.jar .
ENTRYPOINT [ "java", "-jar", "url-builder.jar" ]
```

Now, our image only includes the `openjdk` image plus our own addition of the compiled Kotlin app.

## 3-5 Avoid Golden Images (General Tips)

A "golden image", according to the author, is a locally-modified revision of a canonical build. To give that some more meaning, think of this sordid scenario: you are the producer of an npm module. You went through the proper steps to version the module, but after you publish, you discover an error. Instead of being responsible and publishing a new patched version, which takes some tedious work, you simply make the local modifications necessary to the code, rebuild, and republish the fixed file under the same exact name and version, as if nothing ever happened.

But naturally, later down the line, this desynchronization of source code with deployed code will lead to confusion and frustration. Likewise the same goes for Docker images. Avoid creating or using images that you're afraid to touch because their configuration is baked in, in some mystifying way. An example could be an image that has built in environment variables that were added to the container (which was then committed into an image) in a trial and error debugging session.

So, tips to not fall into this trap:

- Build it from a Dockerfile! Otherwise, if something goes wrong, no one will have no blueprint for it.
- Tag your images with the commit hash of the code that built it.
- If you depend on a piece of software to build your image, check it into your image. (see the dependencies bit earlier)

Also, don't ever leave passwords in layers of your Dockerfile; delete them in the same step.

# Ch 4. Under the Hood

## 4-1 Docker the program

What do kernels do?

- Responds to messages from the hardware
- Start and schedule programs
- Control and organize storage (filesystem)
- Pass messages between programs
- Allocate resources, memory, CPU, network

Docker manages the kernel of the _host system_, that is, the one running Docker Engine, the one running `dockerd`. This means that on my machine, now, even though Docker Desktop is running some small virtualization to enable Linux containers, ultimately the kernel that is being used is still that of my machine. There is no Linux kernel in the Ubuntu distribution. The "Ubuntu" image is just the Ubuntu-specific applications and functionality that live _outside_ of the Linux kernel, i.e., in "userland".

> The term userland (or user space) refers to all code that runs outside the operating system's kernel. Userland usually refers to the various programs and libraries that the operating system uses to interact with the kernel: software that performs input/output, manipulates file system objects, application software, etc.

"Docker never uses a different kernel: the kernel is always your host kernel. If your host kernel is "compatible enough" with the software in the container you want to run it will work; otherwise it won't."
https://stackoverflow.com/a/56606244/6188150

That is why, on this new Apple M1, which uses an entirely different processor architecture, not all images are supported. Images rely on the host machine having some characteristics because it is the host machine's kernel that is ultimately utilized to allocate resources and enable containerization. Because the processor is different, then the kernel, the core OS software program which has a direct line to the processor, is presumably quite different.

And this usage of the host kernel is why Docker is faster than a pure VM.

Back to the course...

Docker uses "cgroups" to contain processes. It uses "namespaces" to contain networks. It uses "copy-on-write" filesystems to build images.

These things were all in existence before Docker, including containerization.

As mentioned earlier, Docker (meaning the Engine) is a client-server architecture. Two programs.

Just because it's client-server doesn't mean its HTTP. In fact, http isn't even technically supported (it's really just `tcp`). They can communicate over other sockets as well, such as a Unix socket. Default is Unix for Mac, TCP for Windows. From Docker:

> The Docker daemon can listen for Docker Engine API requests via three different types of Socket: unix, tcp, and fd.
>
> By default, a unix domain socket (or IPC socket) is created at `/var/run/docker.sock`, requiring either root permission, or docker group membership.

Because this socket is addressable, you can actually point to it from _within_ a Docker container. That would mean to make any use of it, the container itself would also have to have a Docker client!

## 4-2 Networking and namespaces

Networking is comprised of many layers. Fact-check the below!

- Ethernet - moves "frames" on a wire
- IP layer - moves packets on a local network
- Routing - forwards packets between networks
- Ports - addresses particular programs on a computer

Ethernet:
Docker uses "bridges" to create virtual networks in your computer, and control the Ethernet layer. Bridges are not invented by Docker, you can use the `brctl` tool on a Linux system to see bridges. `docker0` is the bridge used by all machines that aren't explicitly attached to a `network` (see earlier lessons). When you create a new network with Docker, Docker also creates a new Linux bridge from the containerized machine to that network.

Routing: Docker creates firewall rules to move packets between networks (uses the `iptables` functionality of Linux). This is NAT (Network Address Translation)

Passing `--privileged=true` to `docker run` gives the container full control of the system that's hosting it.

Passing ports to Docker is just port forwarding at the networking/routing layer within your system.

## 4-3 Processes and cgroups

In Linux, processes come from other processes - a parent-child relationship. When a child process exits, it returns an exit code to its parent.

The process that starts it all, process ID 1, is called `init` (or `launchd` on a Mac), which starts all the other processes (whether directly or indirectly).

So, when your Docker container exits, that means that process ID 1 of that container is killed. The processes of the container are kept separate from the host machine using `cgroups`, which are control groups. Each container has its own `cgroup` on the host machine. Not only does this allow isolation, but also imposing memory/CPU limits on the container. The cgroup for the container must abide by the limits.

> A control group (abbreviated as cgroup) is a collection of processes that are bound by the same criteria and associated with a set of parameters or limits.

Note that even though container processes are isolated from the host and each other by default, the process of your container can share in the pool of other containers or even your host system, with the `--pid` flag.

```
--pid="host"               <- The host's process IDs are shared
--pid="<container>:<name>" <- Process IDs are shared with another container
```

`docker inspect` allows you to see metadata about the container.

Docker images are read only. When you instantiate a container from an image, it adds a "thin read/write layer" that allows you to make changes to it and commit to a new image.

## 4-4 Storage

Let's learn about Unix storage.

From lowest to highest abstraction level:

- Actual storage devices. The actual metal. The devices that the kernel manages.
- Logical storage devices. For instance, in Windows C: and D: are logical storage devices, but the underlying device is the same.
- Filesystems. Which bit on the drive maps to which file.

Docker uses COWs. Copy On Write. This principle leads to the "layering" of images, just like an Adobe program. Or think of Git, with commits only being change instructions - per Docker, "Each layer is only a set of differences from the layer before it." When you download an image, it downloads all the layers separately, as gzip files, then reassembles them. And you can see this in action, with the multiple progress bars.

The author does a poor job, IMO, of explaining Unix filesystem `mount` command. Evidently, this is how Docker volumes work - a host directory is mounted "on top of" a container directory. (And I guess that's how mounting works in general, you mount a horse by getting on top of it.)

What mounting does is change the pointer of a directory.

Say that `/puppies` already exists in your container.

If you make it a volume connected to a host machine directory, then the interior of `/puppies` will mirror the interior of the host directory, pointing to different files. The original files are still there, just not being pointed to. If you unmount (`umount`), then the pointer is removed and the original files underneath become visible again.

# Ch. 5 Orchestration: Building Systems With Docker

## 5-1. Registries in Detail

Supplying `--restart=always` to `docker run` means your container will automatically restart if it crashes.

Docker Hub is built on Docker Registry, which is open source. Generally, if you run a Docker registry yourself, it runs on port :5000. Larger companies often have their own registries.

How would you save your images to an actual file that you could back up or ship to customers?

You can use `docker save`.

```bash
docker save -o my-images.tar.gz jeff-url-builder:v1 jeff-node-countdown:v1
```

And then, when you're ready to use those saved images, you can use `docker load`.

```bash
docker load -i my-images.tar.gz
```

## 5-2 Intro to orchestration

A Docker orchestration system:

- Starts containers, and restarts them if they fail
- Allows containers to find each other ("service discovery")
- Matches containers to the machines best equipped to run them (resource allocation)

The easiest orchestration system is Docker Compose. It's designed for a running multiple containers in a non-distributed system (i.e. 2 or more containers, but still only one host). It's good for testing and development.

For larger systems, Kubernetes is a thing.

- Containers run programs (like Docker)
- Pods group containers together, running on the same system (like Docker Compose)
- Services make pods available to others

With that in mind, there are many big player options for large-scale orchestration.

One is Amazon EC2.

- Task defintion: Define a set of containers that always run together
- Task: runs the containers
- Services expose tasks to the internet and ensure that they're always running, with redundant backups

There are more:

- Amazon Fargate
- Docker Swarm
- Google Kubernetes Engine
- Microsoft Azure Kubernetes Service

## SIDE NOTES

Interesting note: "To put it simply, a container is a virtual machine without a Kernel. Instead, it is using the Kernel of a host operating system."
https://www.freecodecamp.org/news/comprehensive-introductory-guide-to-docker-vms-and-containers-4e42a13ee103/

## Footnotes / Things to Dig into \*\*

How to name your container rather than letting Docker name it for you: `docker run --name`

Name must be unique amongst existing containers, ID is unique all time
