# GNAS-Linuxserver.io HTPC Templates for Portainer

[![Build Status](https://travis-ci.org/gtrummell/gnas-portainer-templates.svg?branch=master)](https://travis-ci.org/gtrummell/gnas-portainer-templates)

This repository hosts Portainer Application Templates for Linuxserver.io
and several other HTPC Application Maintainers, intended for educational
and hobbyist activities.  See the [GNAS-Linuxserver.io HTPC Templates for
Portainer Wiki](https://github.com/gtrummell/gnas-portainer-templates/wiki)
for more information.


## Portainer and Portainer Templates

[Portainer](https://portainer.io/) is an open-source, lightweight management
UI which allows you to easily manage your Docker hosts or Swarm clusters.

The Portainer App Template API and file format documentation are available
at [ReadTheDocs.io](http://portainer.readthedocs.io/en/latest/templates.html).
This repository is patterned after Portainer default templates, available
on [Portainer's Templates repository on GitHub](https://github.com/portainer/templates).


## GNAS-Linuxserver.io Core Apps

GNAS is built using a variety of open-source projects.  All software in this repository reserves their rights as
enumerated in their licenses.  For more information, see:

* [Docker](https://docker.io) container management engine.
* [Portainer](https://portainer.io) Docker container engine manager.
* [LinuxServer.io](https://linuxserver.io), maintainer of respective projects used by these templates.


## Requirements

* [Docker](http://docker.io).
* [Docker-compose](http://docs.docker.com/compose/install/).
* [This repository: GNAS Linuxserver.io HTPC Templates for Portainer](https://github.com/gtrummell/gnas-portainer-templates).


## Usage - Templates

There are several means of using the GNAS-Linuxserver.io HTPC Portainer
templates with Portainer.  They can be loaded via Portainer's UI, or at
the `docker run` command line via the `--templates` switch.

These configurations can be automated in Docker-compose files, or directly
into a NAS via init files (i.e. systemd, upstart, SysV Init)


### Dynamic Container-hosted templates

The default Docker-compose stack in `stacks/gnas-bare-lsio-local` is configured
to build a `portainer-templates` container that serves the default templates
file live to Portainer itself the `portainer-app` container.  This stack
relies on Docker's built-in DNS to allow the app container to find the templates
container.  The templates file can be live-edited and reloaded on disk,
making this stack ideal for local development.

Run it:

```bash
cd stacks/gnas-bare-lsio-local
docker-compose up -d
```


### Remote HTTPS or GitHub-hosted templates

If you prefer not to run your own web service to host template files, another
option is to retrieve the raw file from GitHub.  This example pulls the
GNAS-Linuxserver.io HTPC Portainer Templates as [raw JSON from GitHub](https://github.com/gtrummell/gnas-portainer-templates/blob/master/templates.json).

Run it:

```bash
cd stacks/gnas-bare-lsio-remote
docker-compose up -d
```


### Using custom templates with dynamic Docker-compose stacks

The following steps illustrate how to create your own custom templates
and mount them in a webserver container.

1. Copy an existing template or create a new template and save it to a new
   file (i.e. `foo-stack-templates.json`) and insert your template definitions
   into it.  Make sure this file is accessible to your Docker host.
2. Copy an existing `docker-compose.yml` file, or create a new one.  Configure
   the Nginx `portainer-templates` container to mount the directory containing
   your new template file from the previous step.
3. Run `docker-compose up -d` in the directory containing your new Docker-compose
   file, then log into the Portainer host at http://localhost:9000 and begin using
   your templates!

This is an example `docker-compose.yml` section showing the `portainer-templates`
service mounting a live templates directory on the host system:

```yml
templates:
  container_name: portainer-templates
  image: nginx:stable-alpine
  ports:
    - 8080:80/tcp
  volumes:
    - /path/to/your-templates:/usr/share/nginx/html

app:
  command: [ "--templates", "http://portainer-templates/foo-stack-templates.json" ]
  container_name: portainer-app
  depends_on:
    - templates
  image: portainer/portainer:latest
  ports:
    - 9000:9000/tcp
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock
    - /var/lib/portainer:/data

```

Then run it:

```bash
cd /parent/path-to/docker-compose-file/
docker-compose up -d
```


### Building a Docker image containing templates

If you are distributing your templates across multiple NAS servers, sharing
your templates, or simply backing up your templates for convenient retrieval
during a rebuild, you may want to copy your templates into a Docker image.
The included Dockerfile is an example of how to do so.  For more detailed
instructions, visit [Portainer Documentation on Building Your Own Templates](https://portainer.readthedocs.io/en/stable/templates.html#build-and-host-your-own-templates).

## Usage - Portainer Stacks (Docker-compose files)
   
In addition to Portainer templates that allow you to launch individual containers as
desired, this repository also contains Portainer Stacks.  Portainer Stacks are Docker-
Compose v2.0 files containing a group of applications intended to run together.  The
Portainer Stacks included in this repository can be launched directly from GitHub via
raw source URLs after some setup.  Portainer Stacks included in this repository are
listed below.  See individual READMEs for details.

* **[GNAS EMR Linuxserver.io Stack](stacks/gnas-emr-lsio-remote/README.md):** Launches
  modern "EMR" (Radarr/Sonarr/Lidarr) Media Server stack using
  [this Docker-Compose file](stacks/gnas-emr-lsio-remote/docker-compose.yml).
* **[GNAS Classic Linuxserver.io Stack](stacks/gnas-classic-lsio-remote/README.md):**
  Launches a classic CouchPotato/SickGear Media Server stack using
  [this Docker-Compose file](stacks/gnas-classic-lsio-remote/docker-compose.yml).
* **[GNAS Prometheus Monitoring Stack](stacks/gnas-monitoring-remote/README.md):**
  Launches a complete Prometheus/Grafana monitoring stack using
  [this Docker-Compose file](stacks/gnas-monitoring-remote/docker-compose.yml).
* **[GNAS 415 Linuxserver.io Stack](stacks/gnas-415-lsio-remote/README.md):** Launches
  a fully-loaded modern Media Server stack with Kodi-Headless and Prometheus Monitoring
  using [this Docker-Compose file](stacks/gnas-415-lsio-remote/docker-compose.yml).
* **[GNAS Bare Linxuserver.io Stack](stacks/gnas-bare-lsio-local/README.md):** Launches
  a bare Portainer stack with local Linuxserver.io Templates using
  [this Docker-Compose file](stacks/gnas-bare-lsio-local/docker-compose.yml).
* **[GNAS Bare Linuxserver.io Stack](stacks/gnas-bare-lsio-remote/README.md):**
  Launches a bare Portainer stack with remote Linuxserver.io Templates using
  [this Docker-Compose file](stacks/gnas-bare-lsio-remote/docker-compose.yml).


### Preparing to launch a GNAS Portainer Stack

Launching one of the Portainer Stacks directly from this repository requires a few
setup steps:

**1. Create directories on your NAS for configuration files and storage.**  Issue the
following commands:

  _We are only creating top-level directories for configuration and media.  Docker will
  create subdirectories as needed._
```bash
mkdir -p /opt/gnas \
  /storage/Audio \
  /storage/Documents \
  /storage/Downloads \
  /storage/DVR \
  /storage/Games \
  /storage/Movies \
  /storage/Music \
  /storage/Pictures \ 
  /storage/Transcode \
  /storage/TV \
  /storage/Vides
```

  _We set permissions to nobody:nogroup in order to set a uniform and safe user for all
  media applications.  It may be necessary to run this command again after first run of
  the Portainer stack._
```bash
chown -R nobody:nogroup /opt/gnas /storage
```

**2. Launch Portainer.**  Use Docker-Compose to experiment and Systemd to start
Portainer on system startup.

To launch Portainer with Docker-Compose, see _Usage - Templates_ above.

To launch Portainer on system start, copy its systemd unit file to your server and
start the service.
```bash
cp systemd/portainer.service /etc/systemd/system/
chmod 0644 /etc/systemd/system/portainer.service
systemctl daemon-reload
systemctl enable portainer.service
systemctl start portainer.service.
```

**3. Launch a Portainer Stack.**  Choose a stack from this repository (other than a
bare stack - they contain no apps), or create your own stack.  Then launch it using
Portainer's Stacks UI.  We'll use GNAS EMR as an example.

* Open your portainer UI, for example: http://gnas:9000
* Browse to _Stacks_ in the left menu, then click _+ Add Stack_.
* Choose one of the three available methods to upload your stack to Portainer (_Web
  Editor_, _Upload from your computer_, or _Git Repository_)

Using Web Editor and Upload from your computer are straightforward.  Copy and paste
or choose a Portainer Stack on disk.

Using Git Repository with GNAS EMR would be configured as follows:
* _Repository URL:_ `https://github.com/gtrummell/gnas-portainer-templates`
* _Repository Reference:_ `/refs/heads/master`
* _Compose Path:_ `stacks/gnas-emr-lsio-remote/docker-compose.yml`

## How do I get my media files into my GNAS?
Once you've launched a Portainer Stack, you can get your media into GNAS in one of
several ways:
* If your media files are already organized in a way that is compatible with the
  media managers you want to use, simply move your files into the appropriate
  directories.
* If your files are not organized, you may want to place them into Download
  directories respective of their type (i.e. `/storage/Downloads/audio`,
  `storage/Downloads/movies`, etc.), then use your desired media managers to import
  them.
* If you are simply rebuilding, and your directory structure already matches, then
  step 1 was a no-op for you.  You can simply launch your desired stack.

## Troubleshooting, Notes, and TODOs
* **IT'S PROBABLY PERMISSIONS!** GNAS runs everything as `nobody:nogroup`, or
  `65534:65534`.  This works nicely due to the security properties of nobody/nogroup
  and it is compatible with SAMBA.  Which, by the way...
* **SAMBA/NFS is still run on the host.**  We still run SAMBA4 on the host because it's
  dead simple, bulletproof, and we aren't on Active Directory networks.  You are welcome
  to add a SAMBA container if you prefer.  If you get a good one, let me know and I'll
  include it here.
