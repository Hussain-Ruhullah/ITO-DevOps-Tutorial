# ITO-DevOps-Tutorial
this is a step by step guide on how to host your own gitlab-ce instance with docker

## 0 go to your machine or [Docker-Playground](https://labs.play-with-docker.com/)

## 1 host your own gitlab
```
sudo docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab:Z \
  --volume $GITLAB_HOME/logs:/var/log/gitlab:Z \
  --volume $GITLAB_HOME/data:/var/opt/gitlab:Z \
  gitlab/gitlab-ce:latest
  ```
  ## 2: check logs
  `
  sudo docker logs -f gitlab
  `
  ## 3 get root password
  `
  sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
  `
