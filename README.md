# OE-lite.org Buildbot CI


## Buildbot Master

To build build the buildbot master docker image, run the following command in
the master directory:

```sh
docker build -t buildbot-master .
```

To use latest buildbot-master image, you can don't have to build it, just use
the oelite/buildbot-master image on Docker Hub instead of the local
buildbot-master image shown in the instructions below.

To start a buildbot master docker container:

```sh
docker run -d --name=buildbot-master \
       -p 8010:8010 -p 9989:9989 \
       buildbot-master
```

It will expose the buildbot service on host port 8010.  Test it on
http://localhost:8010/

To check the status (log output) of the buildbot master:

```sh
docker logs buildbot-master
```

To enable persistent state/data of the buildbot master, you should mount a
volume at /srv/buildbot/master.  You can either mount a host directory or use
a data container for the purpose.

If you want to use a host directory, you need to make it owned by UID=999 and
GID=999.  To start master with /var/lib/buildbot mounted, use something like:

```sh
docker run -d --name=buildbot-master \
       -p 8010:8010 -p 9989:9989 \
       -v /var/lib/buildbot:/srv/buildbot/master \
       buildbot-master
```

To use a data container instead, you first need to create a data container:

```sh
docker create --name=buildbot-data oelite/buildbot-data
```

And then use that when starting the master:

```sh
docker run -d --name=buildbot-master \
       -p 8010:8010 -p 9989:9989 \
       --volumes-from=buildbot-data \
       buildbot-master
```


## Buildbot Slaves

To build the buildbot slave docker image, run the following command in the
slave directory:

```sh
docker build -t buildbot-slave .
```

To use latest buildbot-slave image, you can don't have to build it, just use
the oelite/buildbot-slave image on Docker Hub instead of the local
buildbot-slave image shown in the instructions below.

To start a buildbot slave docker container:

```sh
docker run --privileged -d \
       -e SLAVE_NAME=myslavename \
       -e SLAVE_PASSWD=mysecret \
       -e SLAVE_ADMIN="My Name <me@gmail.com>" \
       -e SLAVE_DESCRIPTION="My build slave" \
       --name=buildbot-slave buildbot-slave
```

You should of-course provide proper values for the SLAVE_NAME, SLAVE_PASSWD,
SLAVE_ADMIN and SLAVE_DESCRIPTION variables.

If you need to give some arguments to docker, you can specify them in
DOCKER_DAEMON_ARGS.  If you for example want to use the overlayfs storage
driver:

```sh
docker run --privileged -d \
       -e SLAVE_NAME=myslavename \
       -e SLAVE_PASSWD=mysecret \
       -e SLAVE_ADMIN="My Name <me@gmail.com>" \
       -e SLAVE_DESCRIPTION="My build slave" \
       -e DOCKER_DAEMON_ARGS="-s overlay" \
       --name=buildbot-slave buildbot-slave
```
