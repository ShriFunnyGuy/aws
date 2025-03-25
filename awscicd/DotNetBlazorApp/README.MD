Serving a Blazor WebAssembly App with Nginx in Docker
This guide explains how to serve a Blazor WebAssembly app using a lightweight nginx container. The process involves publishing the Blazor app locally and then building a Docker image to serve the static files.

Prerequisites
.NET 8.0 SDK installed locally.
Docker installed locally.
A Blazor WebAssembly project (e.g., named DotNetBlazorApp).
Step 1: Publish the Blazor App Locally
Navigate to your Blazor project directory in a terminal:
bash

Collapse

Wrap

Copy
cd path/to/DotNetBlazorApp
Run the following command to publish the app in Release mode to a local ./publish folder:
bash

Collapse

Wrap

Copy
dotnet publish -c Release -o ./publish
-c Release: Builds the app in Release configuration for optimized output.
-o ./publish: Outputs the published files (including the wwwroot folder with static assets) to a publish directory at the project level.
Verify that the publish/wwwroot folder contains your app’s static files (e.g., index.html, css, js).
Step 2: Create the Dockerfile
Create a file named Dockerfile in the same directory as your project (where the .csproj and publish folder reside). Add the following content:

dockerfile

Collapse

Wrap

Copy
# Use a lightweight Nginx image based on Alpine Linux for serving static files
FROM nginx:alpine

# Copy the locally published Blazor WebAssembly app's static files from ./publish/wwwroot
# to Nginx's default public directory (/usr/share/nginx/html)
COPY ./publish/wwwroot /usr/share/nginx/html

# Expose port 80 to allow external access to the Nginx web server
EXPOSE 80
Step 3: Build the Docker Image
In the terminal, ensure you’re in the directory containing the Dockerfile and the publish folder.
Build the Docker image:
bash

Collapse

Wrap

Copy
docker build -t blazor-nginx-app .
-t blazor-nginx-app: Tags the image as blazor-nginx-app for easy reference.
.: Specifies the build context as the current directory.
Step 4: Run the Docker Container
Run the container, mapping port 80 in the container to port 8080 on your host:
bash

Collapse

Wrap

Copy
docker run -d -p 8080:80 blazor-nginx-app
-d: Runs the container in detached mode (in the background).
-p 8080:80: Maps port 8080 on your host to port 80 in the container.
Open a browser and navigate to http://localhost:8080 to see your Blazor app.
Notes
nginx Configuration: This basic setup uses nginx’s default configuration, which serves index.html from /usr/share/nginx/html. For client-side routing (common in Blazor WebAssembly), you may need a custom nginx.conf with a try_files directive (see ).
Directory Structure: Ensure your publish folder exists and contains the wwwroot directory before building the Docker image.
.dockerignore: Optionally, add a .dockerignore file to exclude unnecessary files (e.g., bin/, obj/) from the build context:
text

Collapse

Wrap

Copy
bin/
obj/
Advanced Configuration (Optional)
For Blazor’s client-side routing to work (e.g., navigating to /about), add a custom nginx.conf file:

Create nginx.conf in the same directory as your Dockerfile:
nginx

Collapse

Wrap

Copy
events {
    worker_connections 1024;
}
http {
    include /etc/nginx/mime.types;
    server {
        listen 80;
        server_name localhost;
        root /usr/share/nginx/html;
        index index.html;
        location / {
            try_files $uri $uri/ /index.html;
        }
    }
}
Update the Dockerfile to include it:
dockerfile

Collapse

Wrap

Copy
# Use a lightweight Nginx image based on Alpine Linux
FROM nginx:alpine

# Copy the locally published Blazor app's static files to Nginx's public directory
COPY ./publish/wwwroot /usr/share/nginx/html

# Copy the custom nginx configuration to override the default settings
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 80 for the web server
EXPOSE 80
Rebuild and rerun the image.
Troubleshooting
If you see the "Welcome to nginx!" page:
Ensure the publish/wwwroot folder contains your app files.
Add RUN rm -rf /usr/share/nginx/html/* before the COPY step in the Dockerfile to clear default files.
Check container logs: docker logs <container_id>.
Explanation of Changes
Local Publishing: By running dotnet publish locally, you simplify the Dockerfile—no need for a multi-stage build with the .NET SDK. This assumes you have the SDK installed on your machine.
Simplified Dockerfile: Your provided Dockerfile is streamlined to copy the pre-published wwwroot folder and serve it with nginx.
GitHub-Ready: The documentation is structured with clear steps, commands, and optional enhancements, making it easy for others to follow.
Project Structure
After publishing locally, your directory should look like:

text

Collapse

Wrap

Copy
DotNetBlazorApp/
├── Dockerfile
├── DotNetBlazorApp.csproj
├── publish/
│   └── wwwroot/
│       ├── index.html
│       ├── css/
│       ├── js/
│       └── ...
└── (other source files)
