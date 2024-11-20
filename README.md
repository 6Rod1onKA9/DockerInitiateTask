# DockerInitiateTask

1) Once we are done installing Docker, we should test our Docker installation by running the following:

![image](https://github.com/user-attachments/assets/a752760a-f5e1-4290-ab4f-4c259fec8662)

# Hello World

## Playing with Pusybox

2) In this section, we are going to run a Busybox container on our system and get a taste of the `docker run` command. To get started, let's run the following in our terminal:

![image](https://github.com/user-attachments/assets/0bb6a6e8-28ab-4241-b28e-0afdb6123190)

3) The `pull` command fetches the Busybox image from the Docker registry and saves it to our system. You can use the `docker images` command to see a list of all images on your system.

![image](https://github.com/user-attachments/assets/50ad82db-376a-40ad-9cae-e86215c04fea)

## Docker Run

4) Let's now run a Docker container based on this image. To do that we are going to use the almighty `docker run` command.

![image](https://github.com/user-attachments/assets/9deaa310-03b0-464e-88f6-09d07ffdf138)

5) Behind the scenes, a lot of stuff happened. When you call `run`, the Docker client finds the image (busybox in this case), loads up the container and then runs a command in that container. When we run `docker run busybox`, we didn't provide a command, so the container booted up, ran an empty command and then exited.

![image](https://github.com/user-attachments/assets/95bce2ab-7bc5-4c85-96d3-502ae0aea9cc)

6) Finally, we can see some output. In this case, the Docker client dutifully ran the `echo` command in our Busybox container and then exited it. If you've noticed, all of that happened pretty quickly. The `docker ps` command shows you all containers that are currently running.

![image](https://github.com/user-attachments/assets/7c99b7ab-cf46-4e28-9436-6aaac8bab6c2)

7) Since no containers are running, we see a blank line. Let's try a more useful variant: `docker ps -a`

![image](https://github.com/user-attachments/assets/53c26afb-ced0-4ebe-bfd6-a161b9eab800)

8) So what we see above is a list of all containers that we ran. We should pay attention, that the `STATUS` column shows that these containers exited a few minutes ago. Let's try something else:

![image](https://github.com/user-attachments/assets/9a77a9b5-622e-46ce-9562-bd7beb930ce4)

Running the `run` command with the `-it` flags attaches us to an interactive tty in the container.

9) We should always clean up containers, when we're done working with them. To do this, we can run the docker rm command. Just copy the container IDs from above and paste them next to the command.

![image](https://github.com/user-attachments/assets/0f2bc191-70e4-4f63-951b-40d11a80f471)

# Webapps with Docker

## Static Sites

10) The image that we are going to use is a single-page website that I've already created for the purpose of this demo and hosted on the registry - `prakhar1989/static-site`. We can download and run the image directly in one go using `docker run`. As noted above, the `--rm` flag automatically removes the container when it exits and the `-it` flag specifies an interactive terminal which makes it easier to kill the container with `Ctrl+C`.

![image](https://github.com/user-attachments/assets/ecb5b5eb-6772-47f8-a1be-2ab1857465f3)

11) Since the image doesn't exist locally, the client will first fetch the image from the registry and then run the image. If all goes well, you should see a `"Nginx is running..."` message in your terminal. The client is not exposing any ports, so we need to re-run the `docker run` command to publish ports. While we're at it, we should also find a way so that our terminal is not attached to the running container. This way, you can happily close your terminal and keep the container running. This is a called **detached** mode.

![image](https://github.com/user-attachments/assets/99013ba1-a8a7-4463-8652-b8f4221cd66d)

12) In the above command, `-d` will detach our terminal, `-P` will publish all exposed ports to random ports, and finally `--name` corresponds to a name we want to give. Now we can see the ports by running the `docker port [CONTAINER] command`.

![image](https://github.com/user-attachments/assets/bb4b5fc0-9f88-4ac5-8b7c-e93d0ed5e61c)

13) We can also specify a custom port to which the client will forward connections to the container.

![image](https://github.com/user-attachments/assets/f350d50d-7fd2-4345-9c5c-6b8b66ec9c34)
![image](https://github.com/user-attachments/assets/fd751d63-0e76-4997-8f03-76705d45e169)

14) To stop a detached container, run `docker stop` by giving the container ID. In this case, we can use the name `static-site` we used to start the container.

![image](https://github.com/user-attachments/assets/8c568dd8-5760-49ee-b1e9-c33de63d5077)

## Docker Images

15) Docker images are the basis of containers. In the previous example, we pulled the Busybox image from the registry and asked the Docker client to run a container based on that image. To see the list of images that are available locally, use the `docker images` command.

![image](https://github.com/user-attachments/assets/1f64da6f-e5cc-4a06-bb10-17663c09616e)

## Our First Image

16) Now that we have a better understanding of images, it's time to create our own. Our goal in this section will be to create an image that sandboxes a simple Flask application. For the purposes of this workshop, I've already created a fun little Flask app that displays a random cat `.gif` every time it is loaded. If you haven't already, please go ahead and clone the repository locally like so:

![image](https://github.com/user-attachments/assets/a8b62c08-4539-4eb4-afbe-270b61699c7e)
![image](https://github.com/user-attachments/assets/96b78a3e-1ef8-4622-9632-4b9467678db9)

## Docker File

17) Now that we have our Dockerfile, we can build our image. The `docker build` command does the heavy lifting of creating a Docker image from a Dockerfile.

![image](https://github.com/user-attachments/assets/cb3c71b8-5826-4cca-a355-aeef73e358c1)

# Multi-container Environments

## Food Trucks

18) First up, let's clone the repository locally.

![image](https://github.com/user-attachments/assets/0ed2898f-0921-4400-a868-09d021ea8200)
![image](https://github.com/user-attachments/assets/d71dd454-0a74-445e-9a6d-63937638f61c)

19) We can see that the application consists of a Flask backend server and an Elasticsearch service. A natural way to split this app would be to have two containers - one running the Flask process and another running the Elasticsearch (ES) process. That way if our app becomes popular, we can scale it by adding more containers depending on where the bottleneck lies. So we need two containers. We've already built our own Flask container in the previous section. And for Elasticsearch, let's see if we can find something on the hub.

![image](https://github.com/user-attachments/assets/99d8bcac-61e7-4cd7-bbd0-4520b1f252e5)

20) Quite unsurprisingly, there exists an officially supported image for Elasticsearch. To get ES running, we can simply use `docker run` and have a single-node ES container running locally within no time. Let's first pull the image

![image](https://github.com/user-attachments/assets/6192401b-e4e5-4fa1-9b61-47b0f799d487)

21) Then we should run it in development mode by specifying ports and setting an environment variable that configures the Elasticsearch cluster to run as a single-node.

![image_2024-10-29_01-02-52](https://github.com/user-attachments/assets/0173552e-a203-4a3d-8341-0cc6593e6d2b)

22) As seen above, we use `--name es` to give our container a name which makes it easy to use in subsequent commands. Once the container is started, we can see the logs by running `docker container logs` with the container name to inspect the logs. You should see logs similar to below if Elasticsearch started successfully.

![image](https://github.com/user-attachments/assets/3255fa84-3dfa-4946-9311-d3fc6c7a1174)
![image](https://github.com/user-attachments/assets/0ab70127-d18d-4b1c-b903-86a9de13eee8)

23) Finally, we can go ahead, build the image, and run the container.

![image](https://github.com/user-attachments/assets/18d6cb78-66c6-4e3f-90ae-68c6462b6e46)

24) In the first run, this will take some time as the Docker client will download the Ubuntu image, run all the commands, and prepare your image. Re-running `docker build` after any subsequent changes you make to the application code will almost be instantaneous. Now let's try running our app.

![image](https://github.com/user-attachments/assets/843cd144-334f-4496-bc94-6083ed4ffaec)

Oops! Our Flask app was unable to run since it was unable to connect to Elasticsearch.

## Docker Network

25) Let's run `docker container ls` and see what we have.

![image](https://github.com/user-attachments/assets/f0fcad90-8a83-42c9-87a6-70b7163d038b)

26) Now is a good time to start our exploration of networking in Docker. When Docker is installed, it creates three networks automatically.

![image](https://github.com/user-attachments/assets/e665ba11-02ed-4c7b-83d9-b3d51365aba6)

27) The bridge network is the network in which containers are run by default. So that means that when I ran the ES container, it was running in this bridge network. To validate this, let's inspect the network.

![image](https://github.com/user-attachments/assets/f40d8b05-9ac4-4bca-b49e-19d110eac64f)

28) Let's first go ahead and create our own network.

![image](https://github.com/user-attachments/assets/2d4ca45b-f7e5-4e7a-a1cd-f00d35d6537e)

29) The `network create` command creates a new bridge network, which is what we need at the moment. In terms of Docker, a bridge network uses a software bridge which allows containers connected to the same bridge network to communicate, while providing isolation from containers which are not connected to that bridge network. The Docker bridge driver automatically installs rules in the host machine so that containers on different bridge networks cannot communicate directly with each other. Now that we have a network, we can launch our containers inside this network using the `--net` flag. Let's do that - but first, in order to launch a new container with the same name, we will stop and remove our ES container that is running in the bridge network.

![image](https://github.com/user-attachments/assets/b7d5b803-18ec-4fc3-a0c2-2b8fab5fd885)

30) Let's launch our Flask container for real now:

![image](https://github.com/user-attachments/assets/95cadcb6-2d37-4ad3-a1a2-e5cd2c12c173)

31) Let us now stop the services from running. We can always bring them back up in just one command. Data volumes will persist, so it’s possible to start the cluster again with the same data using `docker-compose up`. To destroy the cluster and the data volumes, we have to just type `docker-compose down -v`.

![image](https://github.com/user-attachments/assets/805b0e0b-4726-4db6-a4fe-6c691364fa8d)
