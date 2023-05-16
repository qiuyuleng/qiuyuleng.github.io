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
Steps for compiling TuGraph
docker pull tugraph/tugraph-compile-ubuntu18.04
docker run -it tugraph/tugraph-compile-ubuntu18.04
// In the container
export http_proxy=http://child-prc.intel.com:913
export https_proxy=http://child-prc.intel.com:913
export HTTPS_PROXY=http://child-prc.intel.com:913
export HTTP_PROXY=http://child-prc.intel.com:913
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

Expose container ports 7070 and 9090 to host machine
docker ps
docker commit <container_name_or_id> <new_image_name>
docker stop <container_name_or_id>
docker run -p <host_port>:<container_port> <new_image_name>

Reconnect to container
docker start container_id
docker exec -it container_id bash