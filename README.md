To Deploy into OCP:
-----------------------------
| app layer (foo app)       |
| ------------------------- |
| ops layer (middleware)    |
| ------------------------- |
| busybox                   |
|___________________________|

Base = busybox image from Dockerhub
ops = middleware layer (a shell script)
foo = application layer built from "ops" image

we create builds for the ops and foo applications. We need to wait for the ops:latest image to exist before we can create and build foo, or we could use 
the --allow-missing-imagestream-tags flag on the foo new-app command.

$ oc new-build --context-dir=sh --name=ops --strategy=docker https://github.com/devops-with-openshift/welcome
$ oc new-build --context-dir=foo --name=foo --strategy=docker --allow-missing-imagestream-tags https://github.com/devops-with-openshift/welcome
$ oc create dc foo --image=172.30.18.201:5000/welcome/foo:latest
$ oc expose dc foo --port=8080
$ oc expose svc foo

We can test the running foo application:
$ curl foo-welcome.192.168.137.3.xip.io
Hello foo ! Welcome to OpenShift 3
===================================================================================


###Hello World docker image###

**Build the image**

    docker build -t welcome .
    docker run --name welcome -d -p 8080:8080 welcome

**Login to docker hub**

    virt:~/git/welcome/sh$ docker login 
    Username: devops-with-openshift
    Password: 
    Email:
    WARNING: login credentials saved in /home/devops-with-openshift/.docker/config.json
    Login Succeeded

**Tag the image**

    docker tag -f welcome docker.io/devops-with-openshift/welcome

**Push the image**

    docker push devops-with-openshift/welcome

**Pull the image**

    docker pull devops-with-openshift/welcome
