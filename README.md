# ITO-DevOps-Tutorial
this is a step by step guide on how to host your own gitlab-ce instance with docker

## 0: go to your machine or [Docker-Playground](https://labs.play-with-docker.com/)

## 1: type the commond below to start the gitlab in a container
```
sudo docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 44:22 \
  --name gitlab \
  --restart always \
  --volume /srv/gitlab/config:/etc/gitlab:Z \
  --volume /srv/gitlab/logs:/var/log/gitlab:Z \
  --volume /srv/gitlab/data:/var/opt/gitlab:Z \
  gitlab/gitlab-ce:latest
  ```
  ## 2: check logs to know if the container is running (optional)
  `
  sudo docker logs -f gitlab
  `
  ## 3 get root password
  `
  sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
  `
# UI for Docker
## create a docker volume
`
docker volume create portainer_data
`
## start the portainer
`
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
`

# Gitlab-runner
`
docker run -d --name gitlab-runner --restart always \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:latest
`


## Register the runner
Run the register command :
`docker run --rm -it -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register
`
- Go to your gitlab instance and select settings > ci cd > runners. 
- Enter your GitLab instance URL (also known as the gitlab-ci coordinator URL).
- Enter the token you obtained to register the runner.
- Enter a description for the runner. You can change this value later in the GitLab user interface.
- Enter the tags associated with the runner, separated by commas. You can change this value later in the GitLab user interface.
- Enter any optional maintenance note for the runner.
- Provide the runner executor. For most use cases, enter docker.
- If you entered docker as your executor, you are asked for the default image to be used for projects that do not define one in .gitlab-ci.yml.




version: '3'
services:
  gitlab:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'gitlab.localhost.com'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://gitlab.localhost.com'
    ports:
      - '80:80'
      - '443:443'
      - '44:22'
    volumes:
      - '/srv/gitlab/config:/etc/gitlab'
      - '/srv/gitlab/logs:/var/log/gitlab'
      - '/srv/gitlab/data:/var/opt/gitlab'
    networks:
          - gitlab

  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    depends_on:
      - 'gitlab'
    restart: always
    volumes:
      - '/srv/gitlab-runner/config:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'
    networks:
      - gitlab

networks:
  gitlab:



 create the runner from command line

docker exec -it gitlab_gitlab-runner_1 gitlab-runner register \
--non-interactive \
--url http://gitlab_gitlab_1 \
--registration-token _wgMgEx3nBocYQtoi83c \
--executor docker \
--docker-image alpine:latest \
--docker-network-mode gitlab_default


https://www.czerniga.it/2021/11/14/how-to-install-gitlab-using-docker-compose/
