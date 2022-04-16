# Using Multi-Stage builds

Multi-stage builds are a very efficient way of optimizing the size of your Docker Images, especially when the goal is to build a binary or an executable. The idea behind a multi-stage build is to build intermediate images to compile the code and install dependencies, then copy the necessary binary/executable to another image with only the minimum libraries to run it, thus eliminating the unwanted layers and making the final image much lighter.

They can be used to "clean up" after a development build, by removing the unecessary build tools.

We will now see an example using a simple Go application.

First, go the First_Try directory and make a new Go file :

```
touch main.go
```

Then, paste the following code inside :

```
package main

import "fmt"

func myFunc() string {
	return "DevOps is the U+1F410"
}

func main() {
	fmt.Println(myFunc())
}


```

This is just a code that prints a string that you can try to decode...

Now, run

```
go mod init app
```

This just creates a Go module, which will contain the name of the module and what version of Go was used to build it.

Now we are ready to try and build a Docker image to deploy this application.

Let us first try to do this the way we would normally, by pasting (replacing the previous code) the following in the Dockerfile :

```
FROM golang:1.12.0
ADD . /app
WORKDIR /app
RUN go build
ENTRYPOINT /app/app
CMD ./app
```

Here, we use the golang base image, copy our code inside and build an executable named `App` for it, then we run it.

Build the image using

```
docker build -t your_image_name .
```

Then try running it with

```
docker run your_image_name
```

You can now check the size of the image with

```
docker image ls
```

As you can see, it is quite large. We will now try to reduce the size using a multistage build.

Navigate to the Second_Try directory, and complete the setup like above. Then, paste (replacing the previous code) the following in the Dockerfile.

```
#First step
FROM golang:1.12.0 as build
ADD . /app
WORKDIR /app
RUN go build
ENTRYPOINT /app/app
#Second step
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=build /app/app .
RUN chmod +x /root/app
CMD ./app
```

Here, we first do the same as above, to perform the build and create deployable files. However, we then build a new image, this time using an alpine base image. This is a minimal base image, meaning it is very small. Then, we copy only the required files, i.e the binary, from the previous build to it (which is why we gave it the reference "build"). Finally, we run the app again (Note: we also need to run a command to make the file executable).

Build the image using

```
docker build -t your_image_name .
```

Then try running it with

```
docker run your_image_name
```

Now, check the size again using

```
docker image ls
```

As you can see, the new image is nearly 100 times smaller.
