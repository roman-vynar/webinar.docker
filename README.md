# Docker container with Grafana + Prometheus

Assuming `192.168.56.107` is db node aliased as `ts140i` in Prometheus config.

## Preparation

    git clone ...
    cd webinar.docker

### Create persistent storage (optional):

    mkdir -p docker_shared/{prometheus,grafana}
    chcon -Rt svirt_sandbox_file_t docker_shared  # if running Selinux

## Monitor

### Build docker image:
    
    docker build -t monitor monitor/ 

### Run container:

    docker run -d -p 3000:3000 -p 9090:9090 -e DB_NODE_IP=192.168.56.107 --name prom monitor

Alternatively, with persistent storage:

    docker run -d -p 3000:3000 -p 9090:9090 --name prom \
        -v $PWD/docker_shared/prometheus:/opt/prometheus/data \
        -v $PWD/docker_shared/grafana:/var/lib/grafana \
	-e DB_NODE_IP=192.168.56.107 monitor

## DB node 

### Create MySQL user on the host machine for access by mysqld_exporter:

    mysql> GRANT REPLICATION CLIENT, PROCESS ON *.* TO 'prom'@'%' identified by 'abc123';
    mysql> GRANT SELECT ON performance_schema.* TO 'prom'@'%' identified by 'abc123';

### Build docker image:

    docker build -t dbnode dbnode/

### Run container:

    docker run -d -p 9100:9100 -p 9104:9104 -p 9204:9204 -p 9304:9304 --net="host" -e DATA_SOURCE_NAME="prom:abc123@(192.168.56.107:3306)/" --name exp dbnode 

