# Cluster MySQL with Docker
##Introduction


##Build

    git clone https://github.com/szunaj13pl/docker-mysql-cluster.git
    cd MySQL-Cluster


## Requirements
To set up the MySQL Cluster you need:

* **Docker**
Run command `docker run hello-world` to check if tou have Docker instaled on yout machine
[_Install Docker_](https://docs.docker.com/engine/installation/)

* Proper **network setup**. Every machines need to see each other.
Run command from _HOST_ to _REMOTE_HOST_
`ping -c 3 <remote_host_address> && echo "$(hostname) can connect to $_"` 


Commands explanation
===========

### Docker basic commands

* `stop`        -- Stops choosen container

* `run`         -- Run a command in a new container

  * `--detach`  -- Detached mode: leave the container running in the background

  * `--name`  -- Container name

  * `--rm`  -- Remove intermediate containers when it exits

  * `--net`  -- Connect a container to a network

  * `--publish`            -- Expose a container's port to the host

* `-v`  -- Bind mount a volume

  * `:ro`  -- Volume is in read only mode

* `ps`  -- List containers running containers

  * `-a`  -- List all containers

* `rm`  -- Remove one or more containers

* `rmi`  -- Remove one or more images


### Parameters

`szunaj13/mysql-cluster`  -- Name of image repository

`ndb_mgmd`  -- Management Node

`ndbd`  -- Data Node

`mysqld`  -- SQL Node

# Instalation
## Management Node
---
`docker run --detach --name` <container_name> `--net=host --publish` <ip_managment_node>`:1186:1186 -v` <patch_to_config>`:/etc/mysql-cluster.ini:ro szunaj13/mysql-cluster ndb_mgmd`

_example:_

`docker run --detach --name ndb_mgmd01 --net=host --publish 192.168.0.1:1186:1186 -v config.ini:/etc/mysql-cluster.ini:ro szunaj13/mysql-cluster ndb_mgmd`

---

Data Node
---------

`docker run ---detach --name` <container_name> `--net=host szunaj13/mysql-cluster ndbd `<ip_managment_node>

_example:_

`docker run ---detach --name ndbd01 --net=host szunaj13/mysql-cluster ndbd 192.168.0.1`

---
## SQL Node

`docker run --detach --name` <container_name> `--net=host --publish` <ip_sql_node>`:3306:3306 szunaj13/mysql-cluster mysqld` <ip_managment_node>

_example:_

`docker run --detach --name mysqld01 --net=host --publish 192.168.0.100:3306:3306 szunaj13/mysql-cluster mysqld 192.168.0.1`

---
## Connect to management console

`docker run -it --rm --name` <container_name> `szunaj13/mysql-cluster ndb_mgm` <ip_managment_node>

_example:_

`docker run -it --rm --name ndb_mgm h3nrik/mysql-cluster ndb_mgm 192.168.0.1`

Configuration
=============

## config.ini:
```
   [NDBD DEFAULT]

   NoOfReplicas=2

   DataMemory=80M

   IndexMemory=18M

   datadir=/usr/local/mysql/data


   [NDB_MGMD DEFAULT]

   datadir=/var/lib/mysql-cluster


   [NDB_MGMD]

   NodeId=1

   hostname=192.168.0.1


   [NDBD]

   NodeId=10

   hostname=192.168.0.10


   [NDBD]

   NodeId=11

   hostname=192.168.0.11


   [MYSQLD]

   NodeId=100

   hostname=192.168.0.100


   [MYSQLD]

   NodeId=101

   hostname=192.168.0.101
```
You can easily change configuration

1. Update __config.ini__
2. Stop the __management node__ `docker restart <container_name>`
3. Start the new SQL/data nodes on the hosts configured in the __config.ini__ file.

ENV:
----
|       command                 |       value                   |
|-------------------------------|-------------------------------|
|`MYSQL_CLUSTER_VERSION`        | `7.4`                         |
|`MYSQL_CLUSTER_MICRO_VERSION`  | `12`                          |
|`MYSQL_CLUSTER_ARCH`           | `x86_64`                      |
|`MYSQL_CLUSTER_HOME`           | `/usr/local/mysql`            |
|`MYSQL_CLUSTER_DATA`           | `${MYSQL_CLUSTER_HOME}/data`  |
|`MYSQL_CLUSTER_LOG`            | `/var/lib/mysql-cluster`      |
|`MYSQL_CLUSTER_CONFIG`         | `/etc/mysql-cluster.ini`      |
