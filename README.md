# atom-editor-debian

## Overview
Install (or build) and run the [Atom editor](https://atom.io/) from within
a Docker container.

## To Use or Build ...
Pick whether to use an existing image or build a new one.

### Using Image from Docker Hub
```
docker pull timcera/atom-editor-debian
```

### Building the image
Clone this github repository, change into the source directory and run the
following, replacing 'tag:latest' with something meaningful to you:

```
docker build -t tag:latest .
```

## Running Atom
Make a script, perhaps called 'atom' similar to below.

IMPORTANT: Replace 'tag:latest' in the script with timcera/atom-editor-debian if using
my image on docker hub, or if you built the atom image yourself, the tag that
you gave the build.

```
#!/bin/sh

# Should be platform neutral - at least working on Linux and Windows
USER_NAME=`basename $HOME`

# Pass files on command line to the image via the FFFILES environment variable.
# Need to expand to the full path...
# Files must be in one of the mounted file systems with the -v option.
for file in $@; do
    COLLECT="${COLLECT} `readlink -f ${file}`"
done

# HHHOME is used to pass the HOME directory of the user running qgis
# and is used in "start.sh" to create the same user within the container.

# Users home is mounted as home
# --rm will remove the container as soon as it ends
docker run --rm \
    -i -t \
    -v ${HOME}:/home/${USER_NAME} \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e DISPLAY=unix$DISPLAY \
    -e HHHOME=${HOME} \
    -e FFFILES="${COLLECT}" \
    tag:latest
```

Make the script executable:
```
chmod a+x atom
```
