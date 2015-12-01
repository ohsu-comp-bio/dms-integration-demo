# DMS Integration Demo #

This Docker Compose project allows us to configure multiple services across
multiple "federated" sites to demonstrate data integration strategies. By
running Docker compose on this project, you will get a collection of services
that are accessible via the web browser. They are broken down by institutions.

+ BeatAML
  + [http://dms.beataml.ohsu.dev](http://dms.beataml.ohsu.dev): Data management
    platform via LabKey
  + [https://prime-seq.ohsu.edu/project/CancerStudies/BeatAML/begin.view?](https://prime-seq.ohsu.edu/project/CancerStudies/BeatAML/begin.view?) In development Beat-AML LabKey server.

+ Brenden-Colson
  + [https://bcclabkey.ohsu.edu/project/home/begin.view?](https://bcclabkey.ohsu.edu/project/home/begin.view?) Benden-Colson LabKey server.

+ OICR
  + [http://dms.boutros.oicr.dev](http://dms.boutros.oicr.dev): Data management
    platoform via Postgres + [MyWebSQL](mywebsql.net)

+ CCC
  + Central Function
    + [http://elasticsearch.central-function.ccc.dev](http://elasticsearch.central-function.ccc.dev):
      Search indexing via ElasticSearch, configured as a Tribe node
    + [http://galaxy.central-function.ccc.dev](http://galaxy.central-function.ccc.dev):
      CCC version of Galaxy.
  + OHSU
    + [http://elasticsearch.ohsu.ccc.dev](http://elasticsearch.ohsu.ccc.dev):
      Search indexing via ElasticSearch
    + [Kibana test server](http://e72f625a-bpwalsh.node.tutum.io:5601/app/kibana#/dashboard/aggregated-resource?_g=(refreshInterval:(display:Off,pause:!f,value:0),time:(from:now-15m,mode:quick,to:now))&_a=(filters:!((meta:(disabled:!f,index:'individual-*',key:sex.raw,negate:!f,value:MALE),query:(match:(sex.raw:(query:MALE,type:phrase))))),options:(darkTheme:!f),panels:!((col:1,id:aggregated-resource-donor_age_at_diagnosis,row:1,size_x:3,size_y:2,type:visualization),(col:7,columns:!(ccc_did,url),id:aggregated-resource,row:3,size_x:6,size_y:5,sort:!(_score,desc),type:search),(col:1,id:individual-individuals,row:3,size_x:5,size_y:4,type:visualization),(col:4,id:individual-sex,row:1,size_x:3,size_y:2,type:visualization),(col:7,id:aggregated-resource-project_code,row:1,size_x:2,size_y:2,type:visualization)),query:(query_string:(analyze_wildcard:!t,query:'*')),title:aggregated-resource))
  + OICR
    + [http://elasticsearch.oicr.ccc.dev](http://elasticsearch.oicr.ccc.dev):
      Search indexing via ElasticSearch

Future development will allow for:

- A CCC dashboard app ([Kibana](https://www.elastic.co/products/kibana)) at [http://dashboard.ccc.dev](http://dashboard.ccc.dev)
- A CCC DTS instance at [http://dts.central-function.ccc.dev](http://dts.central-function.ccc.dev)
- ETL apps that run on CCC nodes at
  [http://etl.portland.ccc.dev](http://etl.portland.ccc.dev) and
  [http://etl.oicr.ccc.dev](http://etl.oicr.ccc.dev). These apps manage the
  integration 
- Data generation via [factory_girl](https://github.com/thoughtbot/factory_girl)

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

This approach allows us to configure a "federated" development environment in a
consistent manner. For example, the directory layout of this project looks like:

```
.
├── README.md
├── docker-compose.yml
├── services
│   ├── data
│   │   └── Dockerfile
│   ├── elasticsearch
│   │   ├── Dockerfile
│   │   └── etc
│   │       └── service
│   │           └── elasticsearch
│   │               └── run
│   ├── labkey
│   │   ├── Dockerfile
│   │   └── etc
│   │       ├── labkey
│   │       │   └── labkey.xml.j2
│   │       ├── my_init.d
│   │       │   ├── 50-create-data-dir
│   │       │   └── 50-render-labkey-config
│   │       └── service
│   │           └── tomcat
│   │               └── run
│   ├── mywebsql
│   │   ├── Dockerfile
│   │   ├── etc
│   │   │   ├── nginx
│   │   │   │   └── sites-enabled
│   │   │   │       └── mywebsql.conf
│   │   │   ├── php5
│   │   │   │   └── fpm
│   │   │   │       └── pool.d
│   │   │   │           └── mywebsql.conf
│   │   │   └── service
│   │   │       └── php5-fpm
│   │   │           └── run
│   │   └── opt
│   │       └── mywebsql
│   │           └── config
│   │               └── servers.php
│   ├── postgres
│   │   └── Dockerfile
│   └── proxy
│       ├── Dockerfile
│       └── app
│           └── nginx.tmpl
└── sites
    ├── elasticsearch.central-function.ccc.dev
    │   └── opt
    │       └── elasticsearch
    │           └── elasticsearch.yml
    ├── elasticsearch.ohsu.ccc.dev
    │   └── opt
    │       └── elasticsearch
    │           └── elasticsearch.yml
    └── elasticsearch.oicr.ccc.dev
        └── opt
            └── elasticsearch
                └── elasticsearch.yml
```

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
