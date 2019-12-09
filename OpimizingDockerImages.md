# Using .dockerignore

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

# Remove dev dependencies in the image build steps

Change the Dockerfile to perform additional stuff in order to remove unused or temporary dependencies.

Is it worth? Yes, if you know what you are doing :)