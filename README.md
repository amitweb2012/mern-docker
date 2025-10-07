# Mern Task Management

## Docker Configuration

**Runing complate application and verify**

```
docker compose up --build -d

docker ps -a

```

**Test your application**

```
Open http://localhost:5173 for the frontend
Test API directory http://localhost:5001/api/task for the backend

```

**Push the images to Registry and create tags**

```
docker build -t mern-backend:local ./server
docker build -t mern-frontend:local ./client --build-arg VITE_API_URL=http://localhost:5001/api
docker tag mern-backend:local amitweb2012/mern-backend:1.0.0
docker tag mern-frontend:local amitweb2012/mern-frontend:1.0.0
docker login
docker push amitweb2012/mern-backend:1.0.0
docker push amitweb2012/mern-frontend:1.0.0

```

**Remove local images and tag and run test deployment from registry**

```
docker compose down
docker rmi mern-backend:local mern-frontend:local
docker rmi amitweb2012/mern-frontend:1.0.0 amitweb2012/mern-frontend:1.0.0
docker pull amitweb2012/mern-frontend:1.0.0
docker pull amitweb2012/mern-backend:1.0.0
docker create network mern-net
docker run -d --name demo-mongo --network mern-net -v mongo-data:/data/db mongo:6.0
docker run -d --name demo-backend --network mern-net -e MONGO_URI='mongodb://demo-mongo:27017/taskdb' -p 5001:5001 amitweb2012/mern-backend:1.0.0
amitdas$ docker run -d --name demo-frontend --network mern-net -p 5173:5173 amitweb2012/mern-frontend:1.0.0

```

## Jenkins Setup

**Pull Jenkins images**

```
docker pull jenkins/jenkins:lts

```

**Run Jenkins container with docker mount**

```
docker run -d --name jenkins -u 0\
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins:lts

```

**Install Docker inside Jenkins**

```
docker exec -u 0 -it jenkins bash
apt-get update && apt-get install -y docker.io
usermod -aG docker jenkins
exit
docker restart jenkins
docker exec -it jenkins docker --version
docker exec -it jenkins docker ps -a
```

**Install Docker compose version v2 inside jenkins**

```
docker exec -u 0 -it jenkins bash

mkdir -p ~/.docker/cli-plugins

apt update && apt-get install -y curl

curl -SL https://github.com/docker/compose/releases/download/v2.29.2/docker-compose-$(uname -s)-$(uname -m) \
-o ~/.docker/cli-plugins/docker-compose

chmod +x ~/.docker/cli-plugins/docker-compose

ls -l ~/.docker/cli-plugins/docker-compose

docker compose version

```
