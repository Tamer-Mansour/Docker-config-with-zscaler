## Configuring Docker with Zscaler on Windows

This guide provides step-by-step instructions on how to configure Docker with Zscaler on Windows. By following these instructions, you will be able to build and run Docker containers with Zscaler certificate support.

### Prerequisites

Before you begin, ensure that you have the following prerequisites:

- Docker installed on your Windows machine
- Zscaler certificate file `Zscaler certificate`

### Steps

1. Create folder with name zscaler.
2. Create file with name.
```shell
docker.env
```

```
BUILD_ENV=production
```
or
```
BUILD_ENV=development
```

3. Create file with name.
```shell
docker-compose.yml
```
````
version: '3.1'
services:
  dotnetconf19:
    image: dockersamples/dotnetconf:19
    build:
      context: .
      args:
        - BUILD_ENV=${BUILD_ENV:-`production or development`}
        - CERT_FILE=${CERT_FILE:-/etc/ssl/certs/ca-certificates.crt}
    environment:
      - BUILD_ENV=${BUILD_ENV:-`production or development`}
      - CERT_FILE=${CERT_FILE:-/etc/ssl/certs/ca-certificates.crt}

````
   
4. Create a new file called `Dockerfile` in the project directory.

5. Open the `Dockerfile` using a text editor and add the following content:

```Dockerfile
# Use the base .NET Core SDK 3.0.100-preview9 image
FROM mcr.microsoft.com/dotnet/core/sdk:3.0.100-preview9 AS builder

# Set the working directory in the container
WORKDIR /app

# Copy the project file and restore dependencies
COPY WebRequests.csproj .
RUN dotnet restore

# Copy the entire project files
COPY . .

# Publish the project
RUN dotnet publish -c Release -o /out WebRequests.csproj

# Use the base .NET Core Runtime 3.0.0-preview9 image for the final runtime image
FROM mcr.microsoft.com/dotnet/core/runtime:3.0.0-preview9

# Add the `Zscaler certificate` to the container
ADD `Zscaler certificate` /tmp/`Zscaler certificate`

# Set the BUILD_ENV variable to production
ENV BUILD_ENV=production

# Copy the certificate into the certificate directory and update
RUN if [ "$BUILD_ENV" = "`production or development`" ]; then \
      echo "`production or development` environment"; \
    else \
      echo "Non-production environment: BUILD_ENV"; \
      CERT_DIR=$(openssl version -d | cut -f2 -d \")/certs ; \
      cp /tmp/`Zscaler certificate` $CERT_DIR ; \
      update-ca-certificates ; \
    fi

# Set the working directory in the container
WORKDIR /app

# Set the entry point for the container
ENTRYPOINT ["dotnet", "WebRequests.dll"]

# Copy the published project files from the builder stage
COPY --from=builder /out/ .
```
6. Create `src` folder

7. create `WebRequests.csproj` file into `src`

8. Save and close the `Dockerfile`.

9. Place the `Zscaler certificate` file in the same directory as the `Dockerfile`.

10. Open a command prompt or PowerShell window.

11. Navigate to the directory where your Docker project and `Dockerfile` are located.

12. Build the Docker image by running the following command:

```shell
docker build -t zscaler-image .
```

13. Once the build process is complete, verify that the Docker image was successfully created by running the following command:

```shell
docker images
```

14. Run a Docker container based on the created image using the following command:

```shell
docker run -d --name zscaler-container zscaler-image
```

15. Check if the container is running by executing the following command:

```shell
docker ps
```

Congratulations! You have successfully configured Docker with Zscaler on Windows. You can now interact with the container as needed for your application.

### Additional Notes

- If you are using a different Zscaler certificate file, make sure to update the `ADD` command in the `Dockerfile` accordingly.
- The provided `Dockerfile` assumes a development environment where the Zscaler certificate needs to be added. Adjust the file as per your production environment requirements.

Feel free to customize the instructions or add any additional information specific to your setup or use case.

---

That's it! You have now completed the steps to configure Docker with Zscaler on Windows.
