# Experiment: NGINX Deployment Using Different Base Images

## Objective
Deploy NGINX using different Docker base images and compare image size, layers, and usage.

## Part 1: Official NGINX Image
```
docker pull nginx:latest
docker run -d --name nginx-official -p 8080:80 nginx
curl http://localhost:8080
```

![ ](Screenshots/Screenshot(876).png)

## Part 2: Ubuntu base Image

Dockerfile :
```
FROM ubuntu:22.04
   RUN apt-get update && \
      apt-get install -y nginx && \
      apt-get clean && \
      rm -rf /var/lib/apt/lists/*

  EXPOSE 80
  CMD ["nginx", "-g", "daemon off;"]
```

![ ](Screenshots/Screenshot(877).png)

## Part 3: Alpine Base Image

Dockerfile :
```
FROM alpine:3.18

RUN apk update && apk add --no-cache nginx

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
![ ](Screenshots/Screenshot(878).png)

## Part 4: Image Size and layer Comparison
![ ](Screenshots/Screenshot(879).png)

## Part 5: Using HTML and then cleanup
![ ](Screenshots/Screenshot(880).png)

# Hosting Flask app on docker

## Create docker file

1. create separate folder
2. create a python program:

![ ](Screenshots/fast2.jpeg)

3. create docker file with commands:

```bash

FROM python:3.10-slim

WORKDIR /app

RUN pip install flask

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]


```

![ ](Screenshots/fast6.jpeg)

---

## Build and run image

1. Build the image from the docker file

```bash
docker build -t flask-sapid-app .
```

![ ](Screenshots/fast4.jpeg)




2. Run the image:

```bash
docker run -d -p 8080:5000 flask-sapid-app:3.0
```

![ ](Screenshots/fast5.jpeg)

## Result

Python program running

![ ](Screenshots/fast5.jpeg)
