# Building a Docker Image and pushing it to Docker Hub

One the service is tested locally, now its time build a Docker image and push it to Docker Hub

we recommend you watch our video tutorial on Part 2

![type:video](../video/Datashop tutorial part-2.mp4)

follow the steps below:

**make sure your packages are up to date in requirements.txt file**

```angular2html
pip freeze > requirements.txt
```

**RUN Command to build DOCKER image**


```
docker build -t servicename .
```

**RUN Command to RUN DOCKER container**

```
docker run -d -p 5000:5000 servicename
```

### Test the service locally using docker

Test your service by running thought postman

* Method : POST
* URL : http://localhost:5000/predict
* Body type: JSON

sample payload:
```angular2html
{
 "jobID" : "61ef72ed396fc5330c15f250",

 "dataFileURL":

     {

       "url": "",

       "json":""

     },
"datashopServerAddress": "http://thedatashop.club:8000"
}

```

### Push the image to Docker Hub

ones the image is built and tested locally, now its time push it to Docker Hub

register to Docker Hub and login with your credentials (see [here](https://hub.docker.com/))

use: `docker login` to login to Docker Hub

    docker login -u username -p password

#### step 1: create a new repository on Docker Hub

ones the repository is created, link the image with the repository name using "docker tag" command

    docker tag servicename:latest username/servicename:latest

#### step 2: push the image to Docker Hub

    docker push username/servicename:latest

in short time the image is pushed to Docker Hub and is available to pull
