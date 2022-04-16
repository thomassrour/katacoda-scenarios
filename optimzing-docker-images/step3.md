# Leveraging the Docker Cache

Lastly, we will understand how the Docker cache works, and how we can leverage it to drastically decrease image build time.

As we have seen before, each `RUN`, `ADD` and `COPY` instruction adds a new layer to the image. As such, to reduce build time, Docker stores the cache of each layer. Thus, if the contents of all external files on the first `COPY` or `ADD` instruction are the same, the layer cache will be used and all the following instructions until the next `COPY` or `ADD` instruction will use the layer cache.. However, if the contents of one or more external files are different, then all the following instructions will be executed without using the layer cache.

Let us see an exaple using our old code.

First, navigate to the First_Try directory, and create two new .txt files, and write two different things inside them.

In the directory's Dockerfile, paste (replacing the previous code)the following code :

```
FROM ubuntu:18.04
COPY . your_file_1.txt
RUN apt-get update -y
RUN apt-get upgrade -y
RUN apt-get install vim -y
RUN apt-get install curl -y
RUN apt-get install dnsutils -y

```

Build the image using

```
docker build -t your_image_name .
```

Now change the `COPY` instruction by replacing the .txt file by the other one you created, and build again. Notice that every instruction is re-run.

Then, navigate to the Second_Try directory, and do the same setup as above.

In the directory's Dockerfile, paste (replacing the previous code) the following code :

```
FROM ubuntu:18.04
RUN apt-get update -y
RUN apt-get upgrade -y
RUN apt-get install vim -y
RUN apt-get install curl -y
RUN apt-get install dnsutils -y
COPY . your_file_1.txt

```

Notice that the `COPY` instruction is now after the `RUN` ones. Build the image again, then change the `COPY` instruction like above. You will see that Docker uses the cached layers, thus drastically reducing the image build time.
