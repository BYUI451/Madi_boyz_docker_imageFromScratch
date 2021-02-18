# How to Create a Custom Docker Image from a Python Base Image
### **by Madi and the Boyz:**
**Madi Woodard, Cody Martinson, Dave Moreno**

---

## Install Python and Docker
Click [here](https://www.python.org/downloads/) to download Python.
Click [here](https://docs.docker.com/docker-for-windows/install/) for a guide to install Docker. 

##### *It is also recommended to install [VS Code](https://code.visualstudio.com/download) and its [Docker extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) for convenience.*

---

## Create Virtual Environment
Creating a virtual environment will allow for easier management of installed dependencies for your docker image. Once created, be sure to activate your environment before installing any dependencies. 

### *Anaconda*
If using Anaconda or Miniconda, refer to [this](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) resource on how to create and activate the virtual environment.

### *Windows, MacOS, Linux*
Click [here](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/) for info on creating and activating virtual environments in your operating system.

##### *Windows users might have to change their execution policy in order to activate the virtual environment. See [here](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.1).*

---

## Dependencies
Activate your environment and install all the desired dependencies for your Docker image, usually through `pip` or `conda`. Open the directory where you will create your docker image and create or copy the script(s) that will run in your image. Make sure it runs correctly. Then, enter the following command: 
```bash
pip freeze > requirements.txt
``` 
This will create a new text file `requirements.txt` and write all of your environment's installed packages into it. This document will be referenced during your image building to install all your dependencies in two lines. The list of dependencies can also be entered in manually in the following format.
```
pandas=1.2.2
matplotlib=3.3.*
numpy=1.*
```

---

## Create Dockerfile
In the same directory, create a file called `Dockerfile` with no extension. Some of the common instructions for your dockerfile are listed in the example below. A full list with in-depth explanations can be found [here](https://docs.docker.com/engine/reference/builder/). 

```dockerfile
# Specify the base image that you are using EX.) FROM python:3.8
FROM base_image:tag

# Create a top directory in the container and set it as the default location
WORKDIR /top_directory

# Add files or directories into the top directory upon creation or specify a new filepath within the image
COPY main.py .
COPY other.py file/path/in_image

# Install an individual dependency (used for single packages. See below if there are multiple)
RUN pip install package

# If you use requirements.txt, copy it into the top directory and install all the dependencies 
COPY requirements.txt .
RUN pip install -r requirements.txt

# Open a specific port for your image if need be
EXPOSE 80

# This command will run when the container starts up
CMD python /top_directory/main.py
```
Be sure that your base image is the same version of python as you use in your virtual environment. Otherwise, your dependencies might not work.

##### *See [here](https://docs.docker.com/engine/reference/builder/#dockerfile-examples) for more examples of Dockerfiles.*

---

## Build and Run Image
Build the docker image by running a command in the following format:
```bash
docker build -t image_name:optional_tag path/to/Dockerfile
```
##### *The `-t` argument makes the following text the name of the image and the name must be in lower case. The `optional_tag` is a common convention for identifying image versions but is not necessary.*

After building the image, run the container with a command like so:
```bash
docker run -p 80:80 image_name:optional_tag
```
##### *The `-p` maps the `EXPOSE`'d port from the Dockerfile to the host machine's port. The ports won't work without it.*

---

## Push Image to Dockerhub
First you need to login to your docker by running the command `docker login` and entering your credentials. Then, run the following command:
```bash
docker push image_name:optional_tag
```
If you don't remember your image name or it's too long, you can see your image name and tag by entering `docker images` and copying it into the `docker push` command.

## Congratulations!
You created and pushed your very own python-based docker image.

### Sources:

* https://www.youtube.com/watch?v=bi0cKgmRuiA&amp%3Bt=1000s
* https://ropenscilabs.github.io/r-docker-tutorial/04-Dockerhub.html
* https://nickjanetakis.com/blog/docker-tip-2-the-difference-between-copy-and-add-in-a-dockerile
