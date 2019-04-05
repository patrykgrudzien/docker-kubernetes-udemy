# IT'S MULTI-STAGE BUILD PROCESS

# I'm providing name of that phase (stage) as "builder"
FROM node:8-alpine AS builder
WORKDIR '/app'
COPY package*.json ./
RUN npm install
# according to that change from -> (COPY . .) to -> (COPY . ./) -> look at Section 7, Lecture 93
COPY . ./
RUN npm run build

# Inside the container there will be a directory:
# /app/build
# that we care about

# Next phase (this FROM statement simply terminates each successful block, in our case "builder" one)
FROM nginx
# we haven't uses the (EXPOSE) instruction before in a development environment.
# for me as developer it's only an "information" to read and understand - oo, this container
# probably need a port mapping to port 80. So on my machine, putting that instraction does completely nothing.
# ---
# AWS, elasticbeanstalk is a little bit different. Elasticbeanstalk, when it starts a docker container
# it's gonna look at this Dockerfile for the (EXPOSE) instruction and whatever port is specified here,
# elasticbeanstalk is gonna map to it automatically!
EXPOSE 3000

# copying custom configuration for ngnix that will host (client) project
COPY ./ngnix/default.conf /etc/nginx/conf.d/default.conf

# destination path is specified in "nginx" documentation on the hub.docker.com
COPY --from=builder /app/build /usr/share/nginx/html
# default command to start "nginx" will be loaded automatically and we don't need to call is explicitly