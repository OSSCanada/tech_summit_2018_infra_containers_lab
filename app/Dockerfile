# start with a base image from a container registry
# this one uses a public container image from hub.docker.com
FROM node:9.10.1-alpine

# This is the working directory inside the container
# all commands from this point on are executed from the POV of this directory
WORKDIR /home/node/app

# copy the package.json and package-lock.json file to the working directory
COPY package*.json .

# run the 'npm install' command to install app dependencies
RUN npm install

# copy app source code to the working directory
# NOTE: we use a .dockerignore file included in this directory to avoid copying over
# certain files/folders that would overwrite what "npm install" does for us.  This ensures
# we get a fresh npm dependecy installation each time and for the correct platform/OS.
COPY . .

# Expose the port that the container will use
EXPOSE 3000

# Run the following command when the container is instantiated with "docker run ..." command
CMD ["npm", "start"]

# build the container with the following command
# docker build -t myapp:latest .

# once built you can run the container with the following command
# docker run -it --rm -name demoapp -p 4000:4000 myapp:latest