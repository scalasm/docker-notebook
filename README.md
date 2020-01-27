# (My) Docker notebook

## What it is

This is a collection of my notes about Docker: it comes from several sources, like courses, blog posts, experiences, and so on.
I also include here a collection of references for other Internet resources.

## Why

 I'm sharing these as personal references wherever I go (thanks, GitHub!): maybe the come in handy for some other people too.

## Disclaimer

I'm not responsible for any damage your may incur by following these notes: use them at your risk.

## Index

This is a collection of random notes about docker and container orchestration. Actually we can divide it in two parts:

* [Basic Docker commands, hints and tips](docker/README.md) - this is a collection of snippets useful when dealing with Docker containers.
  * [Docker Compose](docker/DockerCompose.md)
  * [Networking](docker/Networking.md)
  * [Optimizing images](docker/OpimizingDockerImages.md)
  * [Networking](docker/Networking.md)
  * [Volumes and sharing data across containers](docker/SharingDataAcrossContainers.md)
  * [Hits for debugging failed image builds](docker/DebuggingDockerBuildFailures.md)
  * [Profiling your container](docker/ProfilingYourContainer.md)
* [Orchestrating containers with AWS ECS](scaling-docker-on-aws/README.md) - this is a collection of snippets that uses the "Scaling Docker Containers Course" as basic example but its snippets are re-usable elsewhere too!
  * [Task Definitions](scaling-docker-on-aws/TaskDefinitionCommands.md)
  * [Runnig/starting Tasks](scaling-docker-on-aws/TaskCommands.md) - need to start / run / stop / describe tasks?
  * [Services](scaling-docker-on-aws/ServiceCommands.md) - services are long running tasks 
  * [Using ECR as private registry](scaling-docker-on-aws/UsingECR.md) - How to connect and push/pull from ECR 
  * [Terminating your cluster](scaling-docker-on-aws/TaskCommands.md) - clean up everything
  * [Deep Dive sample project](scaling-docker-on-aws/deepdive) - this directory contains resources like JSON files for task definitions used during the sample project
 