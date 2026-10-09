# Mini Finance Dashboard \| Vagrant + Docker

A static finance dashboard packaged as a Docker image and developed
through a local virtualized Linux environment using Vagrant and
VirtualBox. The project demonstrates a practical workflow from local
files to a container image that can be published to Docker Hub.

## Project overview

This project takes the Mini Finance Dashboard website and serves it with
Nginx inside Docker. Vagrant provides a repeatable Ubuntu VM for
building and running the container.

## Architecture

``` text
Windows host
  └── Vagrant + VirtualBox
       └── Ubuntu VM
            └── Docker Engine
                 └── Nginx container
                      └── Static finance dashboard
```

## Tech stack

-   Vagrant
-   VirtualBox
-   Ubuntu 24.04
-   Docker
-   Nginx Alpine
-   HTML, CSS, JavaScript

## Project structure

``` text
Finance dashboard app Docker/
├── Vagrantfile
└── app/
    └── 2135_mini_finance/
        ├── Dockerfile
        ├── .dockerignore
        ├── index.html
        ├── css/
        ├── js/
        ├── images/
        ├── fonts/
        └── pages/
```

Your exact static asset folders may differ. Keep the Dockerfile in the
application folder alongside `index.html`.

## 1. Start the virtual machine

From Windows PowerShell, go to the directory containing the
`Vagrantfile`:

``` powershell
cd D:\virtualmachine\2108-dashboard
vagrant up
vagrant ssh
```

The VM is configured to use Ubuntu 24.04 through the
`bento/ubuntu-24.04` box and VirtualBox.

## 2. Install Docker Engine in Ubuntu

If Docker is not installed in the VM, follow the official Docker Engine
installation instructions for Ubuntu:

https://docs.docker.com/engine/install/ubuntu/

Then verify:

``` bash
sudo docker version
sudo docker info
```

Use `sudo docker` if the `vagrant` user does not have permission to
access the Docker socket.

## 3. Build the image

Inside the Ubuntu VM:

``` bash
cd /home/vagrant/2135_mini_finance
sudo docker build -t mini-finance-dashboard:1.0 .
```

For a multi-stage build, use the multi-stage Dockerfile in the project
folder and choose a new version tag, for example:

``` bash
sudo docker build -t mini-finance-dashboard:2.0 .
```

## 4. Run and test locally

If port `8080` is already occupied by an older container, remove that
container first. For example:

``` bash
sudo docker ps -a
sudo docker rm -f mini-finance-dashboard
```

Then run the image. This maps port `8080` on the VM to port `80` in the
Nginx container:

``` bash
sudo docker run -d \
  --name mini-finance-dashboard \
  -p 8080:80 \
  mini-finance-dashboard:2.0
```

The supplied Vagrantfile forwards host port `8080` to guest port `8080`,
so open this URL in the Windows browser:

http://localhost:8080

Verify the container and HTTP response:

``` bash
sudo docker ps
curl -I http://localhost:8080
```

If a container with the selected name already exists, remove it or
choose another name before running the command.

## 5. Publish the image to Docker Hub

Create or use a Docker Hub repository, for example
`adityaa2510/mini-finance-dashboard`. Sign in from the Ubuntu VM:

``` bash
sudo docker login
```

Tag the image for your Docker Hub account:

``` bash
sudo docker tag mini-finance-dashboard:2.0 adityaa2510/mini-finance-dashboard:2.0
```

Push it:

``` bash
sudo docker push adityaa2510/mini-finance-dashboard:2.0
```

Use a tag that actually exists locally. If you built `1.0`, replace
`2.0` with `1.0` consistently.

Docker Hub repository page:

https://hub.docker.com/r/adityaa2510/mini-finance-dashboard

## 6. Multi-stage Docker build

A multi-stage Dockerfile can separate preparation from the final Nginx
runtime. For a static HTML project, this is primarily a demonstration of
multi-stage build structure; it may not reduce the final image size
compared with a simple Nginx image.

Example:

``` dockerfile
FROM alpine:3.22 AS build
WORKDIR /site
COPY . .
RUN rm -f /site/Dockerfile /site/.dockerignore /site/README-DOCKER.md

FROM nginx:alpine AS production
COPY --from=build /site/ /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Ensure the build stage copies only the files intended for the website. A
`.dockerignore` file can exclude `.git`, local notes, generated reports,
and other unnecessary files.

## Troubleshooting

### `Dockerfile` not found

Run `docker build` from the directory containing the Dockerfile, or
specify the path with `-f`.

### `permission denied` for Docker

Use `sudo docker ...` inside Ubuntu, or configure Docker group access
following Docker's official guidance.

### Container name conflict

Check existing containers:

``` bash
sudo docker ps -a
```

Remove the old container by its actual name:

``` bash
sudo docker rm -f mini-finance-dashboard
```

### Port conflict

Check which container is using the port:

``` bash
sudo docker ps
```

Stop or remove the conflicting container, or choose another host port.

