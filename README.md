
### Go to your machine or [Docker-Playground](https://labs.play-with-docker.com/)

# Docker Compose
## Configuration for Docker Compose
### GitLab/GitLab-runner

1- Ein dediziertes Verzeichnis erstellen, in dem wir Daten und die Gitlab-Konfiguration speichern werden.
`mkdir gitlab`

2- Eine Umgebungsvariable setzen, die den Pfad zu dem Verzeichnis von Gitlab enthält:
`export GITLAB_HOME=$(pwd)/gitlab`

3- Die Datei docker-compose.yml mit folgendem Inhalt erstellen:

```
# docker-compose.yml
version: '3.7'
services:
  web:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'localhost'
    container_name: gitlab-ce
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost'
    ports:
      - '8080:80'
      - '8443:443'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    networks:
      - gitlab
  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    container_name: gitlab-runner    
    restart: always
    depends_on:
      - web
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - '$GITLAB_HOME/gitlab-runner:/etc/gitlab-runner'
    networks:
      - gitlab

networks:
  gitlab:
    name: gitlab-network
    
```
4- Gitlab installation
`docker-compose up -d`

5- Um sich zum ersten Mal bei GitLab anzumelden, benötigt man ein temporäres Passwort, das bei der Installation automatisch generiert wird. Das Passwort wird mit dem Befehl abgefragt:
`docker exec -it gitlab-ce grep 'Password:' /etc/gitlab/initial_root_password` 

### GitLab/runner registrieren
`docker exec -it gitlab-runner gitlab-runner register --url "http://gitlab-ce" --clone-url "http://gitlab-ce"
`
- Go to your gitlab instance and select settings > ci cd > runners. 
- Enter your GitLab instance URL (also known as the gitlab-ci coordinator URL).
- Enter the token you obtained to register the runner.
- Enter a description for the runner. You can change this value later in the GitLab user interface.
- Enter the tags associated with the runner, separated by commas. You can change this value later in the GitLab user interface.
- Enter any optional maintenance note for the runner.
- Provide the runner executor. For most use cases, enter docker.
- If you entered docker as your executor, you are asked for the default image to be used for projects that do not define one in .gitlab-ci.yml.

Nach der ordnungsgemäßen Konfiguration sollten wir die Bestätigung sehen, dass Runner erfolgreich registriert wurde
### Zusätzlich zur Grundkonfiguration müssen wir den vom Runner gestarteten Containern den Zugriff auf das virtuelle Netzwerk erlauben, in dem GitLab arbeitet. Dazu starten wir den Editor (z. B. vi)
`sudo vi gitlab/gitlab-runner/config.toml`


Dann fügen wir eine neue Zeile am Ende der Runner-Konfiguration hinzu: network_mode=“gitlab-network”
