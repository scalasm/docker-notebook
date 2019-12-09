# Optimize your images

But make them work first!

<!-- TOC -->autoauto- [Optimize your images](#optimize-your-images)auto    - [Using .dockerignore](#using-dockerignore)auto    - [Shrink images](#shrink-images)autoauto<!-- /TOC -->

## Using .dockerignore

Edit your *.dockerignore* in order to make **ADD**/**COPY** instructions to skip including the given files/folders from the
final image.
If you have **WORKDIR set**, it will use it as base path!

```
# This will exclude this file from the image
.dockerignore
# Exclude versioning info
.git/

# Other examples
.foo/*
**/*.swp

# Exclude all text files except (!) the special.txt one 
**/*.text
!special.text
```

Another example is 
```
# We ignore all files but the explicitly given here
*

# Then a whitelist follows 
!app.py
!special.txt
!Dockerfile
```

## Shrink images

Remove dev dependencies in the image build steps: everything that is not used at runtime.

Change the Dockerfile to perform additional stuff in order to remove unused or temporary dependencies.

Is it worth? Yes, if you know what you are doing :)