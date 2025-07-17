# Docker Best Practises

In this document , we will dive into some of the best practices for writing docker files and managing Docker images .

## 8 Ways to improve Sercurity , optimize Docker image size and Cleaner and maintainable Dockerfiles .

### 1.Use official Docker Images as Base Image

Using official Docker images as a base image ensures that you start with a wel-maintainable and secure foundation .

### 2. Use Specific Docker image Version

Using specific versions of docker images instead of `latest` helps to avoid unexpected changes in your environment and ensures that your application runs consistently across different deployments.

### 3.Use Small base images

Using small base images like `alpine` or `scratch` reduces the size of your Docker image , which can lead to faster build times and reduced storage costs. Smaller images also have a smaller attack surface (--Vulnerablities), improving security.

### 4.Optimize Caching Image Layers

What is images Layers : Well , Docker imagees are built based on dacker file and each commande in docker file create a image layer , the commade `docker history ${image}` will show you the layers of you built image.
Optimizing caching image layers can significantly speed up the build process. To do this, you should:

- Order commands in your Dockerfile from least to most frequently changing. This is important because when a layer changes, all subsequent layers are rebuilt, which can slow down your build process.
- Combine commands where possible .

### 5.Use .dockerignore file

The `.dockerignore` file allows you to exclude files and directories from being copied into the Docker image. This can help reduce the size of your image and speed up the build process by preventing unnecessary files from being included.

### 6. Make use of multi-stage builds

Usualy during the build time some artifacts are created that are not needed in the final image they are only using on the build time ,
multi-stage builds allow you to seperate the build environment from the runtime environment.
Exemple:

```dockerfile
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp
FROM alpine:13
WORKDIR /app
COPY --from=builder /app/myapp .
CMD ["./myapp"]
```

This example shows how to build a Go application in a separate stage and then copy only the necessary binary into a smaller Alpine image for runtime.
the commande `docker build -t myapp .` will build the image using the Dockerfile in the current directory.

### 7. Use the least privilaged user

Running your application inside the container with root privilages can pose a security risk.
Instead , create a non-root user and switch to that user in your Dockerfile. This reduces the risk of privilege escalation attacks.
Create a dedicated user and group

```dockerfile
FROM alpine:13
RUN groupadd -r mygroup && useradd -g mygroup myuser
USER myuser
```

Note : Some base images like `node` already have bundled generic user .

### 8. Scan Your Images for Vulnerabilities

Execute `docker scan ${image}` to scan your Docker images for known vulnerabilities. This helps you identify and address security issues before deploying your application.

### Conclusion

By following these best practices, you can create Docker images that are more secure, efficient, and maintainable. These practices not only help in reducing the size of your images but also enhance the overall security posture of your applications running in Docker containers.
