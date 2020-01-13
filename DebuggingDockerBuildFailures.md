# Debugging build failures

When docker build fails, you can still take a look at the "partial" container image by starting it.

# Example

My build fails:
```
mario@SCALASM-NB:~/src/links-devops/sparkasse/core-banking-service$ ./build-docker-image.sh
Building docker image  for the project (this may take a while) ...
Sending build context to Docker daemon  29.26MB
Step 1/22 : FROM maven:3-jdk-11 as builder
 ---> a6aca7fe810f
Step 2/22 : RUN mkdir -p /build
 ---> Using cache
 ---> f59017de9513
Step 3/22 : WORKDIR /build
 ---> Using cache
 ---> 3b210f933a5b
Step 4/22 : COPY pom.xml /build
 ---> Using cache
 ---> 849126eb4307
Step 5/22 : RUN mvn -B dependency:resolve dependency:resolve-plugins
 ---> Using cache
 ---> 8b72919b9b78
Step 6/22 : COPY src /build/src
 ---> Using cache
 ---> 3226156d9e44
Step 7/22 : COPY .git/ /build/.git/
COPY failed: stat /var/lib/docker/tmp/docker-builder468433314/.git: no such file or directory
```

The reason is because of the *.git/* folder is not available in the container build image, while still present in the source directory:
```
mario@SCALASM-NB:~/src/links-devops/sparkasse/core-banking-service$ ls .git
COMMIT_EDITMSG  HEAD  config  description  hooks  index  info  logs  objects  refs
```

We can run the **specific image ID** for the given layer and run commands. For example, I want to list the environment variables set in the container:
```
mario@SCALASM-NB:~/src/links-devops/sparkasse/core-banking-service$ docker run -ti 3226156d9e44 env
JAVA_URL_VERSION=11.0.5_10
HOSTNAME=4d059b115037
JAVA_BASE_URL=https://github.com/AdoptOpenJDK/openjdk11-upstream-binaries/releases/download/jdk-11.0.5%2B10/OpenJDK11U-jdk_
HOME=/root
OLDPWD=/usr/share/maven/ref
MAVEN_HOME=/usr/share/maven
JAVA_VERSION=11.0.5
TERM=xterm
PATH=/usr/local/openjdk-11/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
LANG=C.UTF-8
JAVA_HOME=/usr/local/openjdk-11
PWD=/build
```

Or run a SHELL:
```
mario@SCALASM-NB:~/src/links-devops/sparkasse/core-banking-service$ docker run -ti 3226156d9e44 /bin/bash
root@8259260b1d8f:/build# ls
pom.xml  src
root@8259260b1d8f:/build# cd /build/
root@8259260b1d8f:/build# ls
pom.xml  src
root@8259260b1d8f:/build# ls -l
total 12
-rw-r--r-- 1 root root 5975 Jan 10 17:32 pom.xml
drwxr-xr-x 4 root root 4096 Jan 13 10:11 src
root@8259260b1d8f:/build#
```
