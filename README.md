# Docker image for AllegroGraph on Debian Stretch

This Docker image contains AllegroGraph with minimal
configuration that can be easily extended.

## Running

The image is available in the Docker Registry. Here is a sample
command that runs it locally:

    docker run -i -m 4g -p 10000-10035:10000-10035 --shm-size 4g tsznuk/agraph-stretch

Note the following `docker run` arguments used in the above command:

   - `-i` allows us to view AG logs in the terminal.
   - `-m 4g` sets the memory available to the container.  AG needs at
     least ~0.5G of memory per repository.
   - `--shm-size 4g` AllegroGraph uses shared memory and the default
     shared memory size used by Docker (64MB) is insufficient. This
     container will refuse to start without at least 1GB of shared
     memory.
   - `-p` the default configuration in this container makes AG use
     port 10035 as the main port and limits the range of ports used by
     dedicated sessions to 10000-10034. All of these ports must be
     accessible for AllegoGraph clients to work.

## Persisting data

Data stored in the container will be lost when that container is
deleted. To retain your databases create a Docker volume to store it,
mount that volume anywhere in the container and set the
`AGRAPH_DATA_DIR` variable to the mount point.

## Configuring AllegroGraph

The AllegroGraph server configuration file is split into fragments
stored in the `/etc/agraph.conf.d` directory. All files with the
`.conf` extension located in that directory or one of its
subdirectories are concatenated at runtime to form the final
configuration file. Environment variable substitution is performed
during this process.

The configuration can be changed by:
   
   - Mounting more config files at runtime. It is also possible to
     overwrite an existing file in this way.
   - Creating a derived image. In this case all files from the
     `agraph.conf.d` directory will be copied to the right place
     during build.

### Authentication

The `/etc/agraph.conf.d/superuser.conf` file contains hardcoded
superuser credentials. It should be overwritten at runtime by mounting
another file in its place, preferably using Docker secrets.

### License

By default AllegroGraph uses a trial license that will only work for
databases with at most five million triples. To store more triples
obtain a license from Franz Inc. and mount the appropriate config file
fragment in `/etc/agraph.conf.d/` or create a derived image.

## Running additional commands

Docker entrypoint for this image runs all executable files from the
`/etc/docker.entrypoint.d/` directory, in alphabetical order. It is
possible to change the commands by:

   - Mounting more script files at runtime. It is also possible to
     overwrite an existing file in this way.
   - Creating a derived image. In this case all files from the
     `docker.entrypoint.d` directory will be copied to the right place
     during build.
