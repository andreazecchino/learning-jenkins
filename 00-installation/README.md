# Running Jenkins in Docker

Open up a terminal window then:

## 1. Create a bridge network in Docker

```bash
docker network create jenkins
```

## 2. Create a Docker in Docker container

In order to execute Docker commands inside Jenkins nodes.

```bash
docker run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind --storage-driver overlay2
```

## 3. Customize the official Jenkins Docker Image

Create a **[Dockerfile](Dockerfile)** (which install Docker CLI and Jenkins Docker Plugin), then build the image:

```bash
docker build -t myjenkins:2.587 .
```

## 4. Run a container with the new Docker Image

```bash
docker run --name jenkins --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins:2.587
```

## 5. Post-installation setup

To unlock Jenkins, extract and copy the password from the container:

```bash
docker exec ${CONTAINER_ID or CONTAINER_NAME} cat /var/jenkins_home/secrets/initialAdminPassword
```

Then browse `http://localhost:8000`, wait until the **Unlock Jenkins** page appears and then paste the password.

When the **Customize Jenkins** page appears, select the option to install the suggested plugins.

After that, create the first administrator user.

You are ready to start using Jenkins!
