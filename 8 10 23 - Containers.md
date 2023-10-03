# Lab 35 Running Flask in a Docker Container
![[Pasted image 20230810111348.png]]

- Here is the flask script for the application
```python
#!/usr/bin/python3
"""By Chad Feeser | Alta3 Research
To use, try:
    curl localhost:5000/
    curl localhost:5000/atreides/
"""

from flask import Flask
app = Flask(__name__)

# if user sends HTTP GET to /
@app.route("/")
def index():
    return "In Frank Herbert's Dune, the Spice Melange makes space travel possible."

# if user sends HTTP GET to /atreides
@app.route("/atreides")
def atreides():
    return "As Dune opens, House Atreides is transitioning their rule to Arrakis, a desert planet."
    
# bind to all IP addresses port 5000
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)

```

- To transform our application into a containerized app, we need to create a `Dockerfile`. The name `Dockerfile` is actually standardized, so don't try to rename it to something else. This file will tell Docker 'what to do' in order to create our container image.
	- AN image can create multiple containers
```dockerfile
# This base image container is avail on hub.docker.com
# it has python 3.7 avail on Alpine Linux, a minimalist Linux distro
FROM python:alpine3.7

# this "moves" the application/directory (`.` means "this directory we are in" => into /app in the container image) into the container image.
COPY . /app

#this sets the directory copied above as the working directory
WORKDIR /app

# Use Python package installer to install the Flask library to our image
RUN pip install -r requirements.txt

# container is exposed on port 5000
EXPOSE 5000
CMD python ./dune.py
```
- Note that `FROM` directive is pointing to `python:alpine3.7`. This is telling Docker what base image to use for the container, and implicitly selecting what Python version to use, which in this case is 3.7. Docker Hub has base images for almost all supported versions of Python including 2.7. This example is using Python installed on Alpine Linux, a minimalist Linux distro, which helps keep the images small.
- Also note the `RUN` directive that is calling PyPi (pip) and pointing to the `requirements.txt` file. This file contains a list of the dependencies that the application needs to run. Create that file now.

- To build the image, run `docker build` from a command line or terminal that is in the root directory of the application. Including `-t` will "tag" our image as `dune-app`. Including `-f Dockerfile` instructs Docker to look to the file named `Dockerfile` for instructions on how to build the image.
```bash
docker build -t dune-app -f Dockerfile .
```

- Now that our application is built, we can run the image, `dune-app`, within a Docker container named `scifi`.
	- his starts the application as a container. The `--name` parameter names the container and 
	- the `-p` parameter **maps the local host’s port** 5000 **to the container's port** of 5000. 
	- Lastly, the `dune-app` is the image to run (we tagged it with this name). After it starts, you should be able to curl to the container. Try interacting with your endpoints as you did before.
	- `-d` runs the docker container in the background - so it wont hog ip to your command line
```bash
docker run --name scifi -d -p 5000:5000 dune-app
```

- We can look at the containers Docker is currently running with `docker ps`. Issuing this command should show the container `scifi` running the image `dune-app` on port `5000`.
```bash
docker ps
```

- Start ANOTHER instance of your app. The code has been "hardened" to listen on port `5000`. We may "only" have a single instance of port 5000 locally, but we can make Docker port forward **any** local port into the network namespace's port 5000 (where our new Flask application will be running). This time, use port `34727` (locally) and forward to port `5000` (within the container).
```bash
docker run --name sandworm -d -p 34727:5000 dune-app
```

- Because Docker is running our container, we can't 'just' issue a (CTRL + c) on our Flask app to stop it. Any command we would issue will result in Docker simply re-launching the container (Docker's job is to keep applications running). Therefore, we need to tell Docker to stop the container named `scifi`.
```bash
docker stop scifi
```

- Now that our image has been built, it has been cached by Docker locally. To run it, we no longer need to `docker build` the image. We can now simply deploy the built image. Where `dune-app` is the name of the image we created, docker will randomly spawn a name for our container.
	- The only changes should be a new `CONTAINER ID`, as well as a new (randomly created) `NAMES`. In the previous example, we supplied the name via `--name scifi` for our container. T**his time, Docker created one randomly for us**. The following command is a shortcut to stop all containers docker is currently running, regardless of how they are named (WARNING: this will also close Bender, Fry, Zoidberg, and Farnsworth. You'll need to rebuild those if you plan to use them again)
```bash
docker run -d dune-app
```

# Tagging docker images
- docke rimages can have `tags` so the name can stay the same but be: v1 or v2..etc
![[Pasted image 20230810140658.png]]
```bash
docker tag [stuff]
```


- using `latest` IS A BAD IDEA

# Gitlab and COntainers
- Gitlab has its own container registries, yto make your own personal docker hub
- .
```
ssh-keyhe
```