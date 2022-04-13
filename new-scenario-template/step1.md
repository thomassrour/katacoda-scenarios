# Reducing the number of layers.

The first way to optimize your images is to decrease the number of layers you use. One easy way to do that is by minimizing your `RUN`, `COPY` and `FROM` instructions, as each one of these instructions in the Docker file adds a new layer (a new image, but one without a human-assigned ID), thus making the total build time longer and increasing the size of the image.\
We will now see an example.\
Let us try to create a new Ubuntu image, and to install some packages on it that a normal image might use (vim, curl and dnsutil) , first without paying attention to the number of instructions we use, then by minimizing it.
First, go into the `First_Try` package, and add the following to the Dockerfile

```
FROM ubuntu:latest
RUN apt-get update -y
RUN apt-get upgrade -y
RUN apt-get install vim -y
RUN apt-get install curl -y
RUN apt-get install dnsutils -y
```

Then, build the image using

```
docker build .
```

Now, we will try to optimize this, by reducing the installation of all these packages to a single `RUN` instruction. Navigate to the `Second_Try` package, and write the following to the Dockerfile

```
FROM ubuntu:latest
RUN apt-get update -y && apt-get upgrade -y && apt-get install --no-install-recommends vim curl dnsutils -y
```

As you can see, we now only have one `RUN` instruction, which reduces the number of intermediate layers to one. Furthermore, `--no-install-recommends` ensures that recommended packages which aren't dependecies aren't installed, which also optimizes the build.

Then, build the image using

```
docker build .
```

Firstly, you can easily notice that build time dramatically reduces in the second instance. Now, list the images using

```
docker image ls
```

You can notice that the second image is also smaller than the first one, by about 15 MBs.
