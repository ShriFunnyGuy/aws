## Use a lightweight Nginx image
#FROM nginx:alpine
#
## Copy the published Blazor WebAssembly app to Nginx's public directory
#COPY ./publish/wwwroot /usr/share/nginx/html
#
## Expose port 80 to serve the app
#EXPOSE 80

# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /app

COPY ./DotNetBlazorApp.csproj ./DotNetBlazorApp/

RUN dotnet restore

COPY . ./

RUN dotnet publish ./DotNetBlazorApp.csproj -c Release -o out

## Runtime stage
#FROM nginx:alpine
#EXPOSE 80
#COPY --from=build /app/out/wwwroot /usr/share/nginx/html
#COPY nginx.conf /etc/nginx/nginx.conf
#
## Build Stage
#FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
#WORKDIR /app
#
## Copy solution and project files
#
#COPY ./DotNetBlazorApp.csproj ./DotNetBlazorApp/
#
## Restore dependencies
#RUN dotnet restore
#
## Copy all files and build the app
#COPY . ./
#RUN dotnet publish ./DotNetBlazorApp.csproj -c Release -o /app/out
#
## Runtime Stage
#FROM nginx:alpine
#EXPOSE 80
#
## Copy built Blazor WASM app to Nginx default HTML directory
#COPY --from=build /app/out/wwwroot /usr/share/nginx/html
#
##CMD ["nginx", "-g", "daemon off;"]