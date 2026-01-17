# ============================================
# STAGE 1: BUILD STAGE
# ============================================

# Use Node.js 18 Alpine Linux image as the base image for building
# Alpine is a lightweight Linux distribution (~5MB)
# AS build: names this stage "build" so we can reference it later
FROM node:18-alpine AS build

# Set the working directory inside the container to /app
# All subsequent commands will be executed in this directory
WORKDIR /app

# Copy package.json and package-lock.json from your local machine 
# to the current working directory (/app) in the container
# The ./ at the end means "copy to current directory"
COPY package*.json ./

# Install Node.js dependencies
# npm ci (clean install) is faster and more reliable than npm install
# It installs exact versions from package-lock.json
RUN npm ci

# Copy all remaining files from your local machine 
# to the /app directory in the container
# This includes src/, public/, and other project files
COPY . .

# Build the React app for production
# This creates an optimized build in the /app/build directory
# The build is minified, bundled, and ready for deployment
RUN npm run build

# ============================================
# STAGE 2: PRODUCTION STAGE
# ============================================

# Start a new stage using nginx Alpine image
# nginx is a web server that will serve our React app
# This creates a smaller final image (previous stage is discarded)
FROM nginx:alpine

# Copy the built React app from the "build" stage
# --from=build: references the first stage we named "build"
# /app/build: source directory from build stage
# /usr/share/nginx/html: default nginx directory for serving files
COPY --from=build /app/build /usr/share/nginx/html

# Document that the container listens on port 80
# This is informational - it doesn't actually open the port
# You still need -p flag when running: docker run -p 3000:80
EXPOSE 80

# Define the command to run when container starts
# nginx: the web server program
# -g "daemon off;": tells nginx to run in foreground (not as background daemon)
# This keeps the container running (if nginx stops, container stops)
CMD ["nginx", "-g", "daemon off;"]