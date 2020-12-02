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