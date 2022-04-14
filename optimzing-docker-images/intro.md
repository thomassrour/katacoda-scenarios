# Optimizing your Docker images

## Description

Nowadays, Docker (and containeraztion in general) is an essential tool for building and deploying applications. At the root of this technology are Docker images. However, these can very quickly get very large. In this tutorial, you will learn how to reduce both the size of an image and the time it takes to build it through different techniques.

## Learning Objectives

-   How and why to reduce the number of unecessary layers when writing a Dockerfile
-   How to use a multi-stage build, and why it makes the final image much smaller
-   How the Docker cache works and how to leverage it to make the image build faster

## Prerequisite Skills

This tutorial is aimed at people with some experience using Docker, especially Dockerfile writing basics, and DevOps engineers, looking to optimize their Docker images.

## Why it matters for DevOps

When developping software, each new version will require more dependencies and configs, which makes the Docker image build challenging, since the same code will take more time & resources to be built, which is why we should try to reduce the size of the images.

Furthermore, when using container orchestration tools (Kubernetes), we would like to have compact images to reduce the image transfer and deploy time.
