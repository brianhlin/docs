DateReviewed: 2020-06-22

Running StashCache Origin in a Container
========================================

The OSG operates the [StashCache data federation](/data/stashcache/overview), which
provides organizations with a method to distribute their data in a scalable manner to thousands of jobs without needing
to pre-stage data across sites or operate their own scalable infrastructure.

[Stash Origins](/data/stashcache/install-origin) store copies of users' data.
Each community (or experiment) needs to run one origin to export its data via the StashCache federation.
This document outlines how to run such an origin in a Docker container.

Before Starting
---------------

Before starting the installation process, consider the following points:

1. **Docker:** For the purpose of this guide, the host must have a running docker service and you must have the ability
to start containers (i.e., belong to the `docker` Unix group).
1. **Network ports:** The Stash Origin listens for incoming HTTP/S and XRootD connections on ports 1094 and 1095 (by
default).
1. **File Systems:** Stash Origin needs host partitions to store user data.
   For improved performance and storage, we recommend multiple partitions for handling namespaces (HDD), data (HDDs),
   and metadata (SSDs).
1. **Registration:** Before deploying an origin, you must
   [registered the service](/data/stashcache/install-origin/#registering-the-origin) in the OSG Topology

Configuring Stash Origin
------------------------

In addition to the required configuration above (ports and file systems), you may also configure the behavior of your
origin with the following variables using an environment variable file:

Where the environment file on the docker host, `/opt/origin/.env`, has (at least) the following contents (replace
`YOUR_SITE_NAME` with the name of your site as
[registered in Topology](/data/stashcache/install-origin/#registering-the-origin)):

```file
XC_RESOURCENAME=YOUR_SITE_NAME
```

Running an Origin
-----------------

StashCache origin containers may be run with either multiple mounted host partitions (recommended) or a single host
partition.

!!!important "Partition ownership"
    Host partitions mounted into the StashCache origin container must be owned `10940:10940`

### Multiple host partitions (recommended) ###

For improved performance and storage, we recommend multiple partitions for handling namespaces (HDD), data (HDDs),
and metadata (SSDs).

```console
user@host $ docker run --rm --publish 1094:1094 \
             --publish 1095:1095 \
             --volume <HDD NAMESPACE PARTITION>:/xcache/namespace \
             --volume <SSD METADATA PARTITION 1>:/xcache/meta1
             ...
             --volume <SSD METADATA PARTITION N>:/xcache/metaN
             --volume <HDD DATA PARTITION 1>:/xcache/data1
             ...
             --volume <HDD DATA PARTITION N>:/xcache/dataN
             --env-file=/opt/origin/.env \
             opensciencegrid/stash-origin:fresh
```

### Single host partition ###

For a simpler installation, you may use a single host partition mounted to `/xcache/`:

```console
user@host $ docker run --rm --publish 1094:1094 \
             --publish 1095:1095 \
             --volume <HOST PARTITION>:/xcache \
             --env-file=/opt/origin/.env \
             opensciencegrid/stash-origin:fresh
```

!!!warning
    A container deployed this way will serve the entire contents of `<HOST PARTITION>`.

It is recommended to use a container orchestration service such as [docker-compose](https://docs.docker.com/compose/) or
[kubernetes](https://kubernetes.io/), or start the stash origin container with systemd.

### Running on origin container with systemd

An example systemd service file for StashCache.
This will require creating the environment file in the directory `/opt/origin/.env`.

!!! note
    This example systemd file assumes `<HOST PARTITION>` is `/srv/origin`.

Create the systemd service file `/etc/systemd/system/docker.stash-origin.service` as follows:

```file
[Unit]
Description=Stash Origin Container
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
Restart=always
ExecStartPre=-/usr/bin/docker stop %n
ExecStartPre=-/usr/bin/docker rm %n
ExecStartPre=/usr/bin/docker pull opensciencegrid/stash-origin:fresh
ExecStart=/usr/bin/docker run --rm --name %n -p 1094:1094 -p 1095:1095 -v /srv/origin:/xcache --env-file /opt/origin/.env opensciencegrid/stash-origin:fresh

[Install] 
WantedBy=multi-user.target
```

Enable and start the service with:

```console
root@host $ systemctl enable docker.stash-origin
root@host $ systemctl start docker.stash-origin
```

!!! warning
    You must [register](/data/stashcache/install-origin/#registering-the-origin) the origin before considering it a
    production service.



Validating Origin
-----------------

To validate the origin please follow the
[validating origin instructions](/data/stashcache/install-origin/#verifying-the-origin-server).

Getting Help
------------

To get assistance, please use the [this page](/common/help) or contact <support@opensciencegrid.org> directly.
