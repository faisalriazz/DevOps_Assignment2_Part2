# node version 16.17.1
FROM node:16.17.1-alpine3.15 
# Create and set the working directory
WORKDIR /home/app
# Copy package.json and package-lock.json to the working directory
COPY package*.json ./
# install app dependecies
RUN npm install
# copy all contents from the app folder to working directory
COPY .  . 
# EXPOSE the port the app run
EXPOSE 3000
# run the node.js application
CMD ["node","server.js"]

