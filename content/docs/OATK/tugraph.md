---
title: "Tugraph"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

# Steps for compiling TuGraph
```
docker pull tugraph/tugraph-compile-ubuntu18.04
docker run -it tugraph/tugraph-compile-ubuntu18.04
// In the container
export http_proxy=http://
export https_proxy=http://
export HTTPS_PROXY=http://
export HTTP_PROXY=http://
git clone https://github.com/TuGraph-family/tugraph-db.git
git submodule update --init --recursive
cd tugraph-db
deps/build_deps.sh
deps/build_deps.sh --fix
cmake .. -DOURSYSTEM=ubuntu -DENABLE_PREDOWNLOAD_DEPENDS_PACKAGE=1 -DCMAKE_BUILD_TYPE=debug
make
make package
dpkg -i tugraph-3.4.0-1.x86_64.deb
export LD_LIBRARY_PATH=/usr/local/lib64:/usr/local/lib:/usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/:$LD_LIBRARY_PATH
lgraph_server
```

## Expose container ports 7070 and 9090 to host machine
```
docker ps
docker commit <container_name_or_id> <new_image_name>
docker stop <container_name_or_id>
docker run -p <host_port>:<container_port> <new_image_name>
```

## Reconnect to container
```
docker start container_id
docker exec -it container_id bash
```

## Run and debug TuGraph
```
docker run -it -p 7070:7070 -p 9090:9090 --privileged qiuyuleng/tugraph_debug
docker exec -it f4b0fcbe0c0f bash
// In the container
lgraph_server
//get the PID
ps -ef | grep graph

```
## Tugraph Cypher
```
// In the container. Note that before start tugraph_cypher, we need to start lgraph_server and log in beforehand.
python /tugraph-db/src/python/FMA_shell/lgraph_shell/lgraph_cypher.py -c /usr/local/etc/lgraph.json -u admin -P 73@TuGraph
// In lgraph_cypher
CREATE (a:person {name: 'Anne', id: 1235}); // id is the primary key. It has to be unique
```
If cannot parsing json, delete the proxy.

## /usr/bin/python pip module not found when cmake: default python refers to python2.
```
root@ff32e4cc3e1a:/tugraph-db/deps# python --version
Python 2.7.17
root@ff32e4cc3e1a:/tugraph-db/deps# ls -l $(which python)
lrwxrwxrwx 1 root root 9 Apr 16  2018 /usr/bin/python -> python2.7
root@ff32e4cc3e1a:/tugraph-db/deps# rm /usr/bin/python
root@ff32e4cc3e1a:/tugraph-db/deps# ln -s  /usr/bin/python3.6 /usr/bin/python
root@ff32e4cc3e1a:/tugraph-db/deps# ls -l $(which python)
lrwxrwxrwx 1 root root 18 May 16 05:45 /usr/bin/python -> /usr/bin/python3.6
```