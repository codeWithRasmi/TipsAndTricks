# How to run Docker in windows for free 
## Install WSL2 on Windows 

### Open PowerShell as Administrator and run:

> wsl --install

This will:

Enable WSL2

Install Ubuntu by default (you can choose Debian or others too)



#### Restart your system after installation.


### Confirm WSL2 is installed:

> wsl --list --verbose

You should see something like:
```
 NAME      STATE   VERSION
 Ubuntu    Running   2
```


---

### Install Docker inside WSL2

1. Enter Ubuntu shell:

> wsl -d Ubuntu


2. Update packages:

> sudo apt update && sudo apt upgrade -y


3. Install Docker:

> sudo apt install docker.io -y


4. Start and enable Docker service:

> sudo service docker start


5. (Optional but recommended) Add your user to docker group:

> sudo usermod -aG docker $USER

Then restart the shell:

> exit
> wsl


6. Verify:

> docker run hello-world




---

## Deploy Your Small Java Application in Docker

Let’s say you have a small Spring Boot app (or any Java app with jar).

1. In your project folder, create a Dockerfile:
```
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/myapp.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```
<mark> Replace myapp.jar with your actual jar file.</mark>



2. Build the Docker image:

> docker build -t my-java-app .


3. Run the container:

> docker run -d -p 8080:8080 my-java-app


4. Test in browser: Open http://localhost:8080 in Windows → You should see your app running
