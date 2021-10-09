# Alura Dockerswarm

docker ps
docker ps -a
docker swarm init
docker swarm join-token worker
docker node ls
docker node update --availability drain
docker service create
docker service ls
docker service ps
docker service rm
docker service create --mode global
docker service update --replicas 8
docker service update --constraint-add node.role==worker
docker service create --network my_overlay
docker swarm init --force-new-cluster
docker network create -d overlay my_overlay
docker stack deploy --compose-file docker-compose.yml
docker stack ls
docker stack rm