# Jobber with Docker

This image runs Jobber with Docker-in-Docker so you can run in a Docker Swarm and execute one off Docker service replicated jobs.

```yml
version: "3.7"
services:
  scheduler:
    image: trajano/jobber-docker
    deploy:
      resources:
        limits:
          memory: 256M
      placement:
        constraints:
          - "node.role == manager"
    user: root
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    configs:
      - source: jobfile
        target: /home/jobberuser/.jobber
        uid: "1000"
        gid: "1000"
        mode: 0400
configs:
  jobfile:
    file: ./example.yml
```

Example job file with docker command

```yml
version: 1.4
jobs:
  DockerJob:
    cmd: |
        docker service create \
            --mode replicated-job \
            fullstorydev/grpcurl:latest \
            -d '{"name":"foo"}' \
            ca1:6565 \
            rpc.Greeter/SayHello
    time: "*"
    notifyOnSuccess:
      - type: stdout
        data:
          - stdout
```
