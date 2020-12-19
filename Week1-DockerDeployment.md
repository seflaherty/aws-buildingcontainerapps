# [Building Containerized Apps on AWS](https://www.coursera.org/learn/containerized-apps-on-aws/home/welcome) @coursera

## Week 1: Docker CLI

### tldr;

### Corrections

RE: Docker Files and Semantics, there are two pieces of information that are out of date. The first being that the `MAINTAINER` instruction is now deprecated. Instead of using the MAINTAINER instruction you should be using the `LABEL ` instruction which allows you to add metadata to an image. See the following: 

`LABEL maintainer="luser@notarealemail.com"` 

The second being that the `ENTRYPOINT` instruction can be overridden at runtime via the `--entrypoint` flag. Read more about the entrypoint flag here: https://docs.docker.com/engine/reference/builder/#entrypoint 

In our example, we define python as the `ENTRYPOINT` and `app.py` as the `CMD`. `CMD` is used to feed default parameters to `ENTRYPOINT`, so in our case when the container executes it will run the command: `python app.py` to start our flask application. 

Read more about the ways `CMD` and `ENTRYPOINT` interact here: https://docs.docker.com/engine/reference/builder/#cmd

### Docker basic commands

```bash
voclabs:~/environment $ docker version --help

Usage:  docker version [OPTIONS]

Show the Docker version information

Options:
  -f, --format string       Format the output using the given Go template
      --kubeconfig string   Kubernetes config file
```

/You are going to start in AWS' Cloud9 console. This is a "warm up" query to docker that 1.) It is available! 2.) It has a response to your flagged query./

```bash
voclabs:~/environment $ docker info
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 7
 Server Version: 19.03.13-ce
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: c623d1b36f09f8ef6536a057bd658b3aa8632828
 runc version: ff819c7e9184c13b7c2607fe6c30ae19403a7aff
 init version: de40ad0 (expected: fec3683)
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 4.14.203-116.332.amzn1.x86_64
 Operating System: Amazon Linux AMI 2018.03
 OSType: linux
 Architecture: x86_64
 CPUs: 1
 Total Memory: 985.5MiB
 Name: ip-10-16-10-80
 ID: TIWG:LN5G:L6XE:JFLU:CTGC:NZBN:2DBW:L3FR:QNJL:U2BJ:SS4L:KXQ3
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false
```

/Getting a bit more technical info wrt your lab environment./

```bash
 voclabs:~/environment $ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
lambci/lambda       python3.8           e6cf0b46b26b        3 weeks ago         522MB
lambci/lambda       nodejs12.x          55dbbd9e27c9        3 weeks ago         388MB
lambci/lambda       nodejs10.x          b4e0a7118f05        3 weeks ago         383MB
lambci/lambda       python3.7           e1ad51bc7730        3 weeks ago         943MB
lambci/lambda       python3.6           32f70d79634b        3 weeks ago         891MB
lambci/lambda       python2.7           c1be52177e41        3 weeks ago         761MB
lambci/lambda       nodejs8.10          b77afdbde40f        3 weeks ago         813MB
voclabs:~/environment $ docker images --no-trunc
REPOSITORY          TAG                 IMAGE ID                                                                  CREATED             SIZE
lambci/lambda       python3.8           sha256:e6cf0b46b26be77aa018919b31ef2fa9b0901f236e9c583ed26d505145bcfd04   3 weeks ago         522MB
lambci/lambda       nodejs12.x          sha256:55dbbd9e27c99f86630df219bc3fa8c888b8a01a91b981f60e409c04ec1ec2f1   3 weeks ago         388MB
lambci/lambda       nodejs10.x          sha256:b4e0a7118f05c2bb5a01a29ecab9481b194be5a10d7d5935c1e078d33e9a5903   3 weeks ago         383MB
lambci/lambda       python3.7           sha256:e1ad51bc7730a82c2b1912039c6e68c2e5861aa216c66af57a0913277699086a   3 weeks ago         943MB
lambci/lambda       python3.6           sha256:32f70d79634bca0b3308031fe32ba9f5cfc88bf9682fd9ed39e940b24b59c93e   3 weeks ago         891MB
lambci/lambda       python2.7           sha256:c1be52177e41d892a51664394eb7fa5eefaf045bb5babb555c26f453dc04efec   3 weeks ago         761MB
lambci/lambda       nodejs8.10          sha256:b77afdbde40fe0384674b543aeceab94c600b6a6d2494f9a337be9be57a3e753   3 weeks ago         813MB
```

/We have several defaults already included in the lab environment. We will be adding more. Note how you can view non-truncated output in the console./

```bash
voclabs:~/environment $ docker build --tag scare_me .
Sending build context to Docker daemon  37.38kB
Step 1/2 : FROM alpine
latest: Pulling from library/alpine
801bfaa63ef2: Pull complete 
Digest: sha256:3c7497bf0c7af93428242d6176e8f7905f2201d8fc5861f45be7a346b5f23436
Status: Downloaded newer image for alpine:latest
 ---> 389fef711851
Step 2/2 : COPY boo.txt /boo_inside_my_container.txt
 ---> f9d3894f82b2
Successfully built f9d3894f82b2
Successfully tagged scare_me:latest
```

/In Cloud9, we can build a very simple Dockerfile and textfile in the editor. Using the small (< 6MB) Alpine image, we can build a new Docker image with said textfile as it's very special content./

```bash
REPOSITORY          TAG                 IMAGE ID                                                                  CREATED              SIZE
scare_me            latest              sha256:f9d3894f82b299524b2a7cf7a4db5c67ecbcf1a0f18439d3711e8c7cee5647e7   About a minute ago   5.58MB
alpine              latest              sha256:389fef7118515c70fd6c0e0d50bb75669942ea722ccb976507d7b087e54d5a23   44 hours ago         5.58MB
. . . (as before)
```

/The latest alpine image is now know to us, and there is our tagged image too./

### To Do: Create and Deploy a Python Flask App

The code has been provided and it resides in the experiments folder. Look at the `app.py` file as well as the `Dockerfile`.  You can also get a better understanding of [how the program will work](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-flask.html) with elastic beanstalk. 

```dockerfile
FROM python:3.6
COPY . /appdir
WORKDIR /appdir
RUN pip install Flask==1.0.2
EXPOSE 5000
CMD["python", "app.py"]
```

/Our pretty small DockerFile/

```python
from flask import flask

def say_hello(username = "World"):
    return '<p>Hello %s!</p>\n' % username

header_text = '''
    <html>\n<head> <title> Elastic Beanstalk Flask app</title> </head>\n<body>'''

instructions = '''
    <p><em>Hint</em>: This was designed as a <a href="https://en.wikipedia.org/wiki/Representational_state_transfer">RESTful</a> web service, so just append a name to return a greeting to them.</p>''' 

home_link = '''
    <p><a href="/">Go Back</a></p>'''

footer_text = '''
    </body></html>'''

# Elastic Beanstalk looks for an 'application' callable by default
application = Flask(__name__)

# Add a rule for the index page
application.add_url_rule('/', 'index', (lambda: header_text + say_hello() + instructions + footer_text))

# Add a rule when the page is accessed with a name appended to the site URL
application.add_url_rule('/<username>', 'hello', (lambda username: header_text + say_hello(username) + home_link + footer_text))

# Run application
if __name__ == "main__":
    application.debug = True # This is not a **Production** app!
    application.run(host:"0.0.0.0")
```

/Our simple, non-Production, Flask app/

```bash
voclabs:~/environment/experiments/flask $ docker build --tag flask-tutorial .
voclabs:~/environment/experiments/flask $ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
flask-tutorial      latest              0667089d5031        16 seconds ago      884MB
scare_me            latest              f9d3894f82b2        9 minutes ago       5.58MB
python              3.6                 bd4a91d81d7e        14 hours ago        874MB
alpine              latest              389fef711851        44 hours ago        5.58MB
. . .
```

/Our Flask-tutorial DockerFile builds upon the python 3.6 set. This is now available to our lab environment./

```bash
voclabs:~/environment/experiments/flask $ docker run -d --name flask-tutorial -p 5000:5000 flask-tutorial
```

/Doing something right away (so we don't get bored. The `-d` switch detaches the run from our terminal so we can continue to use it. Other features are port mapping (5000 to 5000); naming the running instance; and of course, the image name./

```bash
voclabs:~/environment/experiments/flask $ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
a227b6312c2c        flask-tutorial      "python app.py"     8 seconds ago       Up 7 seconds        0.0.0.0:5000->5000/tcp   flask-tutorial
```

/Things check out. Within the six properties, we can map them back to our terminal commands./

```bash
voclabs:~/environment/experiments/flask $ curl http://localhost:5000/jewels

    <html>
<head> <title>EB Flask Test</title> </head>
<body><p>Hello jewels!</p>
<p><a href="/">Back</a></p>
</body>
```

/Flask-tutorial is a simple REST app. It echoes the input as HTML./

```bash
voclabs:~/environment/experiments/flask $ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS                    NAMES
a227b6312c2c        flask-tutorial      "python app.py"     About a minute ago   Up About a minute           0.0.0.0:5000->5000/tcp   flask-tutorial
4ea3be605b56        scare_me            "bin/sh"            11 minutes ago       Exited (0) 10 minutes ago                            hardcore_dijkstra
```

/Flask-tutorial is a running, named app. Scare_me was running for a moment (actions not shown), but we had passed in a call for an interactive session `docker run -ti scare_me bin/sh` and exited, thus starting and then ending the runtime./

### Next Steps: Elastic Compute Repository and other AWS integrations

ECR and cover that it is a fully managed Docker container registry service.  Which makes it easy to store, manage and deploy Docker container images.  You tell them that it is integrated with Elastic Beanstalk and ECS (which you will cover later) which also simplifies the process when you deploy to production.

```bash
voclabs:~/environment/experiments/flask $ aws ecr get-login --region us-west-2 --no-include-email
docker login -u AWS -p eyJwYX... == https://159818119522.dkr.ecr.us-west-2.amazonaws.com
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /home/ec2-user/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

/Obtain a login handle from ECR via the CLI. The response is everything you need. Copy & paste the whole response back into the Cloud9 terminal to login  (although its not the preferred way to do this-- b\c security)./

```bash
voclabs:~/environment/experiments/flask $ aws ecr create-repository --repository-name flask-tutorial
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:us-west-2:159818119522:repository/flask-tutorial",
        "registryId": "159818119522",
        "repositoryName": "flask-tutorial",
        "repositoryUri": "159818119522.dkr.ecr.us-west-2.amazonaws.com/flask-tutorial",
        "createdAt": 1608322732.0,
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": false
        },
        "encryptionConfiguration": {
            "encryptionType": "AES256"
        }
    }
}
```

/Create the ECR repository for out image. Note that the registryId/repositoryUri is important for later as that value is needed to reference your image in future deployment./

> ProTip: Re-tag your image to include your registryId.  This makes it easier to manage if you have many accounts or work with other registry providers like docker hub, et al.

```bash
docker tag flask-tutorial:latest 159818119522.dkr.ecr.us-west-2.amazonaws.com/flask-tutorial
```

/Making that tag available for us./

```bash
voclabs:~/environment/experiments/flask $ docker push 159818119522.dkr.ecr.us-west-2.amazonaws.com/flask-tutorial
The push refers to repository [159818119522.dkr.ecr.us-west-2.amazonaws.com/flask-tutorial]
d10138f098cc: Pushed 
1c1e2bb91972: Pushed 
68f83665b4cd: Pushed 
0d59c820d67a: Pushed 
238ce87f4664: Pushing [==================================================>]  46.27MB
068ec1375999: Pushing [============================================>      ]  15.97MB/17.95MB
da87e334550a: Pushing [=>                                                 ]  14.27MB/510MB
c5f4367d4a59: Waiting 
ceecb62b2fcc: Waiting 
193bc1d68b80: Waiting 
f0e10b20de19: Waiting 
...
latest: digest: sha256:65f8a21582c3a1dc72c7eaedd1bef77bf0bd489e7709f279457489976b275941 size: 2636
```

/Perform the push across AWS services./

```bash
voclabs:~/environment/experiments/flask $ aws ecr list-images --repository-name flask-tutorial
{
    "imageIds": [
        {
            "imageDigest": "sha256:65f8a21582c3a1dc72c7eaedd1bef77bf0bd489e7709f279457489976b275941",
            "imageTag": "latest"
        }
    ]
}
```

/Showing that the flask image is now stored in ECR./

### Playing Mr. Deployment

1. From the top-level of the flask folder, create a zipfile of its contents on the console.
2. On the left panel expand the flask folder and right click on the flask_application.zip and choose Download from the Cloud9 file viewer.
3. Open the **Elastic Beanstalk** service console from AWS search.
4. At the top right choose Create Application.
5. Paste in flask-app for Application name.
6. For Platform choose Docker.  Leave the docker settings as is.  
7. For Application code choose Upload your code.  Leave Local file selected.  Choose Choose File and browse to where the flask_application.zip was downloaded.  
8. Choose Create application.  
9. Eventually you should see the Health go to Ok with a green circle and a check mark to indicate everything is healthy. (Hint: _I never did._)
10. At the top you should see your application as well with the URL.  Click that and at the top you should see the Environment ID and URL. Copy the URL.
11. Choose the URL (which should open in a new browser tab) and you should see your example Flask Application.
    /If it :pointup had worked, there would have been a live deploy via Beanstalk. In the Events log, you would have seen things done in AWS on your behalf to make this all magically "run": Was EC2 created? VPC, too? What about Security group? App is open at port 80? What about Load Balancing (HA)?/

### Readings and Resources (excerpted from the course)

[Docker Basics](https://docs.docker.com/engine/docker-overview/) To fully understand the Docker engine, familiarize yourself with the Docker architecture and the technology underlying it. Docker uses a client-server architecture. The main components that make up this architecture is the Docker daemon, the Docker client, and Docker registries.
You will use Docker to create and manage images, containers, networks, volumes, and more

[DockerFile](https://docs.docker.com/engine/reference/builder/) In order to have an executable container image, you need to create a text file known as a Dockerfile. Dockerfiles contain instructions on how to build a Docker image step by step. There are many different keywords that you will use to write instructions. When the Dockerfile is built, each instruction is executed in order and the end result is an executable Docker image.

[Docker Images](https://docs.docker.com/storage/storagedriver/) Docker uses Union File Systems and the copy on write strategy for managing files and layers. 

The use of the copy on write strategy helps keep the top writable layer small, copying files into the top writable layer only when needed.

[Docker CLI and Logging](https://docs.docker.com/engine/reference/commandline/docker/) The Docker CLI is a tool that developers can use to interact with the Docker engine. It allows you to build containers, run containers, view container logs, and stop your containers, all through a terminal. 

When you use the CLI, you’ll type a command into your terminal that starts with docker. After the base command, you can use whichever child command you’d like, such as docker **build**, docker **logs**, or docker **run**.

[AWS Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker.html) AWS Elastic Beanstalk provisions the needed infrastructure for you, installs and manages the runtime of your application, and places your running application onto an EC2 instance(s). AWS Elastic Beanstalk supports containerized applications in addition to various supported languages and runtimes. AWS Elastic Beanstalk supports multiple containers for a multi-container environment by provisioning and managing an Amazon Elastic Container Service cluster on your behalf. 

[Amazon Elastic Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) A strong understanding of container image registry utilization is going to be very important as you learn about, and begin your use of, containers. Amazon Elastic Container Registry provides a lot of useful features and great benefits as an image registry, and you should familiarize yourself with at least its basic concepts as soon as possible
