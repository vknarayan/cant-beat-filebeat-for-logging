# cant-beat-filebeat-for-logging
Filebeat for Docker container logging

There are different approaches to using filebeat for collecting logs from containers.

Approach 1: filebeat is used at the node level. Filebeat is running in a standalone container. We make use of the filebeat input type
"docker". Here, filebeat in the container reads the container logs from the folder "/usr/share/filebeat/containers" which is mapped ( bind mounted) from the host folder /var/lib/docker/containers. Filebeat reads the logs from all containers in the host. It can also be configured to read from specific containers only by putting their ids under "containers.ids:" in the filebeat.yml file. Refer above for filebeat configuration file.

Approach 2: Filebeat can be run as a microservice on the host and collect logs directly from /var/lib/docker/containers.

Approach 3: Filebeat can reside inside each container and collect logs from respective sources ( Filebeat inputs type: log). The benefit is that it has no host dependencies ( accessing /var/lib/docker/containers or its bind mount) and so can easily extend to working in virtual / cloud environments. The con is the extra process load per container. Also, difficulties in monitoring filebeat secondary service and ensuring that the container is not terminated if the filebeat terminates. Also, ensuring terminating of filebeat when the primary process ( micro service) in the container terminates. This can be addressed by bash job control.

The scripts & config files for Approach 1 are attached in this repository. Here, filebeat is running in a container. We make use of the filebeat input type "docker". Here, filebeat in the container reads the container logs from the folder "/usr/share/filebeat/containers" which is mapped ( bind mounted) from the host folder /var/lib/docker/containers. Filebet reads the logs from all containers in the host. It can also be configured to read from specific containers only by putting their ids under "containers.ids:" in the k9_docker_filebeat.yml file

---------------- common commands-----------------------------------

docker build -f k9_filebeat_dockerfile -t custom-filebeat .

docker container run -d -e host_UID=$UID --name custom-filebeat-container -v /var/lib/docker/containers:/usr/share/filebeat/containers custom-filebeat

docker container exec -it custom-filebeat-container bash
