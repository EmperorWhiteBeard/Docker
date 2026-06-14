# Docker & Containers: A Beginner's Guide

## What is a Container?

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings.

**In simple terms:** A container is a bundle of your application, the libraries it needs to run, and the minimum system dependencies — nothing more, nothing less.

![Container Diagram](https://user-images.githubusercontent.com/43399466/217262726-7cabcb5b-074d-45cc-950e-84f7119e7162.png)



## Containers vs Virtual Machines

Containers and virtual machines are both technologies used to isolate applications and their dependencies, but they have some key differences:

1. **Resource Utilization:** Containers share the host operating system kernel, making them lighter and faster than VMs. VMs have a full-fledged OS and hypervisor, making them more resource-intensive.

2. **Portability:** Containers are designed to be portable and can run on any system with a compatible host operating system. VMs are less portable as they need a compatible hypervisor to run.

3. **Security:** VMs provide a higher level of security as each VM has its own operating system and can be isolated from the host and other VMs. Containers provide less isolation since they share the host operating system.

4. **Management:** Managing containers is typically easier than managing VMs, as containers are designed to be lightweight and fast-moving.



## Why Are Containers Lightweight?

Containers are lightweight because they use a technology called containerization, which allows them to share the host operating system's kernel and libraries while still providing isolation for the application and its dependencies. This results in a smaller footprint compared to traditional virtual machines, as containers do not need to include a full operating system. Additionally, Docker containers are designed to be minimal — only including what is necessary for the application to run.

**Example:** The official Ubuntu base image used for containers is just ~22 MB. In contrast, the official Ubuntu VM image is close to ~2.3 GB. The container base image is roughly 100 times smaller.

![Ubuntu Base Image Size](https://user-images.githubusercontent.com/43399466/217493284-85411ae0-b283-4475-9729-6b082e35fc7d.png)


### Files and Folders in Container Base Images

```
/bin   - Binary executables (ls, cp, ps, etc.)
/sbin  - System binary executables (init, shutdown, etc.)
/etc   - Configuration files for various system services
/lib   - Library files used by binary executables
/usr   - User-related files and utilities (apps, libraries, documentation)
/var   - Variable data such as log files, spool files, and temp files
/root  - Home directory of the root user
```


### Files and Folders Containers Use from the Host OS

```
Host file system   - Accessible via bind mounts (read/write files on the host)
Networking stack   - Host's network provides connectivity to the container
System calls       - Host kernel handles system calls (CPU, memory, I/O access)
Namespaces         - Provides isolation for file system, process IDs, and network
Control groups     - Limits and controls resource usage (CPU, memory, I/O)
```

> **Note:** While a container uses resources from the host OS, it remains isolated from the host and other containers. Changes inside a container do not affect the host or other containers.

In summary, container base images are significantly smaller than VM images because they are designed to be minimalist and only contain the components needed to run a specific application. VMs, on the other hand, emulate an entire operating system — including all libraries, utilities, and system files — resulting in a much larger size.



## Docker

### What is Docker?

Docker is a containerization platform that provides an easy way to containerize your applications. Using Docker, you can:
- Build container images
- Run images to create containers
- Push containers to registries such as Docker Hub, Quay.io, and more

In simple terms: **containerization is the concept or technology**, and **Docker is the tool that implements it**.


### Docker Architecture

![Docker Architecture](https://user-images.githubusercontent.com/43399466/217507877-212d3a60-143a-4a1d-ab79-4bb615cb4622.png)

The Docker daemon (`dockerd`) is the brain of Docker. If the daemon stops working for any reason, Docker stops functioning entirely.


### Docker Lifecycle

There are three core commands in the Docker lifecycle:

1. `docker build` → Builds a Docker image from a Dockerfile
2. `docker run`   → Creates and runs a container from an image
3. `docker push`  → Pushes the image to a public or private registry

![Docker Lifecycle](https://user-images.githubusercontent.com/43399466/217511949-81f897b2-70ee-41d1-b229-38d0572c54c7.png)


### Key Terminology

#### Docker Daemon
The Docker daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.

#### Docker Client
The Docker client (`docker`) is the primary way users interact with Docker. When you run commands such as `docker run`, the client sends these commands to `dockerd`, which carries them out.

#### Docker Desktop
Docker Desktop is an easy-to-install application for Mac, Windows, or Linux that enables you to build and share containerized applications and microservices. It includes the Docker daemon, Docker client, Docker Compose, Docker Content Trust, Kubernetes, and a Credential Helper.

#### Docker Registries
A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is configured to look for images there by default. You can also run your own private registry.

- `docker pull` / `docker run` → Images are pulled from your configured registry
- `docker push` → Your image is pushed to your configured registry

#### Dockerfile
A Dockerfile is a text file where you define the steps to build your Docker image.

#### Images
An image is a read-only template with instructions for creating a Docker container. Images are often based on another image, with some additional customization. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild, only the changed layers are rebuilt — making images lightweight and fast.



## Installing Docker

Detailed installation instructions are available at: https://docs.docker.com/get-docker/

**For a quick setup on an Ubuntu EC2 instance (AWS):**

```bash
sudo apt update
sudo apt install docker.io -y
```


### Start Docker and Grant Access

A common beginner mistake is forgetting to start the Docker daemon and grant the appropriate user access after installation.

**Verify your Docker installation:**

```bash
docker run hello-world
```

If you see an error like this:

```
docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock
```

This means either:
1. The Docker daemon is not running, or
2. Your user does not have permission to run Docker commands.


### Start the Docker Daemon

Check whether the daemon is running:

```bash
sudo systemctl status docker
```

If it is not running, start it:

```bash
sudo systemctl start docker
```


### Grant Your User Access to Docker

Add your user to the `docker` Linux group (created automatically during installation):

```bash
sudo usermod -aG docker ubuntu
```

Replace `ubuntu` with your actual username.

> **Note:** You need to log out and log back in for this change to take effect.


### Verify Docker is Up and Running

```bash
docker run hello-world
```

Expected output:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```



## Hands-On: Build and Run Your First Container

### Clone the Repository

```bash
git clone https://github.com/EmperorWhiteBeard/Docker
cd Docker/examples
```

### Log in to Docker Hub

[Create a free account at Docker Hub](https://hub.docker.com/) if you haven't already.

```bash
docker login
```

```
Username: your-dockerhub-username
Password:
Login Succeeded
```

### Build Your First Docker Image

Replace `yourname` with your Docker Hub username:

```bash
docker build -t yourname/dockerimage:latest .
```

Example output:

```
Sending build context to Docker daemon  131.1kB
Step 1/7 : FROM python:3.11-slim
Step 2/7 : WORKDIR /app
Step 3/7 : COPY requirements.txt .
Step 4/7 : RUN pip install --no-cache-dir -r requirements.txt
Step 5/7 : COPY . .
Step 6/7 : ENV NAME=World
Step 7/7 : CMD ["python3", "app.py"]
Successfully built 086a2f16a03e
Successfully tagged yourname/dockerimage:latest
```

### Verify the Image Was Created

```bash
docker images
```

```
REPOSITORY                  TAG       IMAGE ID       CREATED          SIZE
yourname/dockerimage        latest    960d37536dcd   26 seconds ago   467MB
ubuntu                      latest    58db3edaf2be   13 days ago      77.8MB
hello-world                 latest    feb5d9fea6a5   16 months ago    13.3kB
```

### Run Your First Docker Container

```bash
docker run -it yourname/dockerimage:latest
```

```
Hello World
```

### Push the Image to Docker Hub

```bash
docker push yourname/dockerimage:latest
```

```
The push refers to repository [docker.io/yourname/dockerimage:latest]
896818320e80: Pushed
b8088c305a52: Pushed
69dd4ccec1a0: Pushed
latest: digest: sha256:6e49841ad9e720a7baedcd41f9b666fcd7b583151d0763fe78101bb8221b1d88 size: 1157
```

Your image is now public and shareable with anyone. Great work — you've just built, run, and published your first Docker container!
