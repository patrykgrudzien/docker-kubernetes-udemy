#### Docker and Kubernetes: The Complete Guide
[Udemy link](https://globallogic.udemy.com/docker-and-kubernetes-the-complete-guide/learn/v4/content)


### Notes from the course:

---

#### <u>Section 1-4:</u>
1. List containers:
```docker ps```
2. List all containers (included stopped ones):
```docker ps --all (--a, -a)```
3. Remove all containers from the disk: 
```docker system prune```
4. ```docker run = docker create + docker start```
5. ```docker start + <container_id>```
6. Start container and print last process (command)<br>
    ```markdown
    docker start -a <container_id>
    docker start -a 54fsdf234dfas echo Hello World
    ```
    * Note to above commands:
        * **`(echo Hello World)`** - cannot override default command used at container creation
        * `docker run <image_id> npm run test` - **`(npm run test)`** here can override default 
        command which was specified in Dockerfile as container is <u>**not yet created**</u>
7. Get logs from container: `docker logs <container_id>`
8. Execute an additional command in a container: `docker exec -it <container_id> <command>`
9. Dockerfile most important instructions:
    1. **`FROM`**
    2. **`RUN`**
    3. **`CMD`**
10. Remove all images: `docker rmi $(docker images -a -q)`
11. Remove single image: `docker rmi <image_id>`
12. `docker run -it <image_id>`**`sh`**
    * **`(sh)`** - overriding default command on **`run`** with **`sh`**
13. **`WORKDIR /usr/app`** - any following command will be executed relative to this path in the container


#### <u>Section 5 (Docker Compose with Multiple Local Containers):</u>
1. **`Docker compose`** - separate **CLI** that gets installed along with Docker. Used to start up multiple
Docker containers at the same time. Automates some of the long-winded arguments we were passing to `docker run`.
    * `docker run <image_id> = docker-compose up`
    * `docker build . + docker run <image_id> = docker-compose up --build`
    * Launch in the background: `docker-compose up -d`
    * Stop containers: `docker-compose down`
    * `docker ps = docker-compose ps` (it can be run in the folder that contains `docker-compose.yml`)
2. docker build with custom Dockerfile name: `docker build -f <file_name>`


#### <u>Section 6 (Creating a Production-Grade Workflow):</u>
1. Docker <u>**volume**</u>:
    * Inside container we no longer copy content from local directory.
    * We essentially set up a **`placeholder`** of sorts inside of a docker container. And by doing so, we’re no longer 
    going to copy over the entire **`src`** directory or the entire **`public`** directory from the local machine to docker container. 
    Instead, you can kind of imagine that we’re going to put in a kind of **`reference`** inside container. 
    The volume is essentially going to set up a reference that’s going to **`point back to our local machine`**.
    * **`docker run -p 3000:3000 -v /app/node_modules`** - put a bookmark **`reference`** on that folder , it's necessary
    as locally I deleted **`node_modules`** folder and container reference would point to nothing in such case and by doing
    that syntax - without **`:`** equals sign after **`/node_modules`**, we tell docker that we want this folder to be a
    placeholder for a folder that is inside a container - we have **`node_modules`** inside a container after execution 
    **`npm install`** command 
    * **`-v $(pwd):/app <image_id>`** - mark, connect the **`pwd`** from the local machine into the **`/app`** folder inside
    the container. **`pwd`** location contains all local source code that we're working on.
2. **Lecture 75-76 (Live Updating Tests)** - we're creating a container using **`docker run -it <image_id> npm run test`**, 
specifically to run tests and we take a snapshot of all our working files and folders and put them inside a container.
So that's  **TEMPORARY** container doesn't have any **VOLUMES** set up! And that's the issue which causes tests to don't 
refresh automatically when we change or add them.
    * <u>**First**</u> solution (more verbose):
        1. Run: `docker-compose up`
        2. Attach to running container with `npm run test` command using:
            * `docker exec -it <container_id> npm run test`
        3. Downside to this approach is that we need to remember order of commands and `<container_id>`
    * <u>**Second**</u> **`(annotation#1)`** solution would be to set up another `service` inside `docker-compose` only to run `test`:
        1. Name: `test`
        2. **No ports mapping**
        3. Downside to this approach is that we get logging results to web app (in the same console) that exposes port
        to outside world and we <u>**we cannot attach to standard input process**</u> to operate interactively with
        container running tests.
3. **Lecture 77** - when we run `docker attach <container_id>` we always attach to 
<u>**standard in (stdin) of the primary process (with ID=1) of the container!**</u><br>
That's why, accordingly to **`(annotation#1)`** we cannot interact with results from `test` service (container) as inside,
after listing all processes with `ps` command, we can see that there are many processes running and that one responsible
for executing tests, **`is not the primary one!`**


#### <u>Section 9 ("Dockerizing" Multiple Services):</u>
1. In `package.json` files, there is a script: `"dev": "nodemon"`
    * `nodemon` is a little command line tool that can be used to automatically reload my entire project whenever source
    code inside is changed.
    
    
#### <u>Section 10 (A Continuous Integration Workflow for Multiple Images):</u>
1. All content is mostly included in **`*.png`** screenshots
2. **Lecture 131** - delete test content from `./complex/client/src/App.test.js`


#### <u>Section 11 (Multi-Container Deployment to AWS):</u>
1. All content is mostly included in **`*.png`** screenshots
2. Note to **`amazon-EB-4.png`** & **`amazon-EB-5.png`** screenshots - instructions how to define `Container Definition` 
point to `task definition` inside **`Amazon Elastic Container Service (ECS)`** and we need to look into documentation 
how this structure of instructions must look like.
3. In the development environment, we use AWS services:
    1. AWS Elastic Cache
    2. AWS Relational Database Service
    * **Note:** I'm not creating separate containers for `postgres` & `redis` as those provided by AWS have handful 
    set of features (look at screenshots).
4. **Lecture 148** - important to set `ENVIRONMENT VARIABLES` as we did in `docker-compose.yml`
    * When we worked with `docker-compose.yml` we had to add `environment` section where we listed all environment variables
    for specific service e.g. `api` & `worker`.
    * In case of AWS ElasticBeanstalk, when we set up those env variables - they are got automatically added to **all different containers**
    that were defined in the `Dockerrun.aws.json` file ! 
5. **Lecture 148** - updates to `.travis.yml` file:
    1. Once we push our images to `Docker Hub`, we need to "notify" `AWS` to pull those images and deploy
    2. The only file that we have to send over to AWS is the `Dockerrun.aws.json` file
6. **Lecture 151** - we must specify at least one of `memory` or `memoryReservation` setting for `client` container definition
    (<u>**we'll do it for each service/container definition**</u>) 
    1. In other case, we'll see an **ERROR** inside AWS console !
7. **Lecture 155** - remember to shutdown:
    * EB Instance
    * RDS (Postgres)
    * EC (Redis)
    * Security Group (VPC -> Security Group) - `Optional`
    * IAM - `Optional`


#### <u>Section 12 (Onwards to Kubernetes!):</u>
1. Question that we may ask ourselves here is - how could we scale our application on AWS?
2. Elastic Beanstalk by default creates <u>**additional copies of the entire set of containers !!!**</u>
3. As developers, we're interacting with a `Kubernetes Cluster` by reaching to the ->  **`Master`** (master controls what each Node does)
4. **Commands:**
    1. `minikube start` - it's gonna automatically access the copy of `VirtualBox`, it's gonna use it to set up the virtual machine and configure 
    this machine to behave as `Kubernetes` `Node`
    2. `minikube status`
    3. `kubectl cluster-info`
    
 