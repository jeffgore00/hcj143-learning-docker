# Learning Docker

Platform: LinkedIn Learning (formerly Lynda)
Instructor: Arthur Ulfeldt
URL: https://www.linkedin.com/learning/learning-docker-2018

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

That server side of docker is called the _docker daemon_. And the client side is the docker CLI. The daemon is accessible via a REST API.

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

- `docker images` will list all the images on your machine.

## 2-3 Run processes in containers

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

Will start a new `bash` process in the already running "soft_baby" container.

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



## Footnotes / Things to Dig into \*\*

How to name your container rather than letting Docker name it for you: `docker run --name`

Name must be unique amongst existing containers, ID is unique all time
