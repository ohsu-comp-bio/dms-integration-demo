# DMS Integration Demo #

This Docker Compose

## How it works ##

Docker Compose is used to define services that are accessible as sites. This
works by creating the association between a LabKey instance and a site like
[http://dms.beataml.ohsu.dev](http://dms.beataml.ohsu.dev). At this point, it's
easy to spin up multiple copies of a service and deploy them locally to other
sites such as [http://dms.boutros.oicr.dev](http://dms.boutros.oicr.dev). For
customizing each site, theres a `sites` directory that we can mount to `/sites`
in each container. The contents of that directory is copied to the root
directory, allowing you to install custom services and init scripts at
container runtime.

## Requirements ##

- Docker 1.9.x
- Docker Compose 1.5.x
- Custom `/etc/hosts` entry that maps `dev` to your Docker Machine instance.
  If on OS X, you can manage this a lot easier with [Pow](https://pow.cx).

## Running ##

This configuration uses Docker 1.9's networking features to do inter-container
communication. To enable this networking configuration in Docker Compose, you
will need to enable the "experimental networking" option:

```
docker-compose --x-networking up
```

This will automatically build and launch all services.
