## Compose sample application
### NGINX proxy with Go backend

Project structure:
```
.
├── backend
│   ├── Dockerfile
│   └── main.go
├── compose.yaml
├── proxy
│   └── nginx.conf
└── README.md
```

[`compose.yaml`](compose.yaml)
```
services:
  proxy:
    image: nginx
    volumes:
      - type: bind
        source: ./proxy/nginx.conf
        target: /etc/nginx/conf.d/default.conf
        read_only: true
    ports:
      - 80:80
    depends_on:
      - backend

  backend:
    build:
      context: backend
      target: builder
```
The compose file defines an application with two services `proxy` and `backend`.
When deploying the application, docker compose maps port 80 of the frontend service container to the same port of the host as specified in the file.
Make sure port 80 on the host is not already in use.

## Deploy with docker compose

```
$ docker compose up -d
Creating network "nginx-golang_default" with the default driver
Building backend
Step 1/7 : FROM golang:1.13 AS build
1.13: Pulling from library/golang
...
Successfully built 4b24f27138cc
Successfully tagged nginx-golang_proxy:latest
Creating nginx-golang_backend_1 ... done
Creating nginx-golang_proxy_1 ... done
```

## Expected result

Listing containers must show two containers running and the port mapping as below:
```
$ docker compose ps
NAME                     COMMAND                  SERVICE             STATUS              PORTS
nginx-golang-backend-1   "/code/bin/backend"      backend             running
nginx-golang-proxy-1     "/docker-entrypoint.…"   proxy               running             0.0.0.0:80->80/tcp
```

After the application starts, navigate to `http://localhost:80` in your web browser or run:
```
$ curl localhost:80

          ##         .
    ## ## ##        ==
 ## ## ## ## ##    ===
/"""""""""""""""""\___/ ===
{                       /  ===-
\______ O           __/
 \    \         __/
  \____\_______/

	
Hello from Docker!
```

Stop and remove the containers
```
$ docker compose down
```

## Use with Docker Development Environments

You can use this sample with the Dev Environments feature of Docker Desktop.

![Screenshot of creating a Dev Environment in Docker Desktop](../dev-envs.png)

To develop directly on the services inside containers, use the HTTPS Git url of the sample:
```
https://github.com/docker/awesome-compose/tree/master/nginx-golang
```
