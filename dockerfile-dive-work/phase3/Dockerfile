# Project: Build a React app and serve it with nginx

#the frontend react files exist in our project folder
#but we have no backend code.
#use the nginx image and incorporate our react files into that
#so it can act as out backend web server,
#making a completed fron + backend react image

# BUILD STAGE
# use node to install dependencies
# and run the 'npm run build' script to compile the react app into static files

# Step 1: Base image for build stage - use official node image with alpine base
#         Name it build-stage
FROM node:16-alpine AS build-stage

# Step 2: Set the working directory to /app
WORKDIR /app

# Step 3: Copy in all the files needed to install dependencies
COPY *.json .

# Step 4: Install dependencies using npm
#   To keep the image small, (force) clean the npm cache after
#   Chain the commands to reduce the number of layers in the image
RUN npm install \
  && npm cache clean --force


# Step 5: Copy in all the files from the current directory
COPY . .
#first . is the current host directory, where the dockerfile is
#second . is the WORKDIR location
#so this copies all the pre-existing react files in oue project folder
#into the new /app folder where the image is told to place it's stuff


# Step 6: Build application
RUN npm run build
# this is a script defined in package.json that uses the 'react-scripts build' command
# this builds the react javascript files into static html/css/js files.
# ('static' in this sense means files that don't require a backend server to
# generate content. it is still dynamic react syle content.
# the files are static, but the browser content is dynamic due to javascript)
# these static files can be passed to nginx, and node is no longer needed because we are done using javascript
# the result of this is a /build folder with these production ready files
# that folder is what we will pass to nginx

# react uses modern javascript, but the 'build' script
# transpiles that into plain, browser-compatible javascript.
# that's why node is no longer needed, even though javascript is still in the files


#-------------------

#TRANSFER STAGE
# grab the nginx image and pass it our static files.
# don't copy over the node image, we don't need it

# when you start a new FROM command, it starts a NEW image layer chain
# so our previous image pull (of node) will not be included in the final image!
# but we can still reference the files it made for us in the following build stage

# only the final FROM line determines your final image base.
#Earlier stages are discarded unless explicitly referenced with COPY --from=...

# Step 7: Bring in the base image for NGINX (alpine)
FROM nginx:alpine

# (There will be no need to EXPOSE a port because this base image
# already has an EXPOSE command)

# Step 8: Set working directory to the html folder for nginx
#   (Hint: This directory was also used in phase 1)
WORKDIR /usr/share/nginx/html

# Step 9: Copy over the build files from build-stage
#   The build directory was created inside the app directory in the
#   build-stage. The files inside that folder can be put directly into
#   the html folder that you just set as your working directory
COPY --from=build-stage /app/build .

# Step 10: Replace the default NGINX config with the application's version
#    The absolute path to the default NGINX config file is
#    /etc/nginx/conf.d/default.conf —replace it with the nginx.conf file
#    provided in this folder
COPY nginx.conf  /etc/nginx/conf.d/default.conf

# (No need to add a CMD because it's included in the base image)

HEALTHCHECK CMD curl -f http://localhost/ || exit 1
