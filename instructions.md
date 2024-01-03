-- Install Docker / Docker Compose
https://docs.docker.com/desktop/install/mac-install/

-- Create file & folder structure
mkdir -p ~/gitlab/config ~/gitlab/logs ~/gitlab/data

-- Create config file (docker-compose.yml)
version: '3'

services:
  web:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'your-gitlab'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://your-gitlab-hostname'
        nginx['redirect_http_to_https'] = true
        nginx['ssl_certificate'] = '/etc/gitlab/nginx.crt'
        nginx['ssl_certificate_key'] = '/etc/gitlab/nginx.key'
    ports:
      - '443:443'
      - '80:80'
      - '22:22'
    volumes:
      - '~/gitlab/config:/etc/gitlab'
      - '~/gitlab/logs:/var/log/gitlab'
      - '~/gitlab/data:/var/opt/gitlab'

-- Create self-signed certificate
openssl req -newkey rsa:2048 -nodes -keyout ~/gitlab/config/nginx.key -x509 -days 365 -out ~/gitlab/config/nginx.crt

-- Run Gitlab Self-managed
docker-compose up -d
Add to /etc/hosts the following line:
127.0.0.1   your-gitlab

-- WAIT......

-- Access Gitlab SM from browser
https://your-gitlab
(if you get connection closed, keep waiting)


--- Restart docker
docker-compose down
docker-compose up -d

-- Default Gitlab password
docker-compose logs | grep 'Password:'
 or
docker exec -it <container_id> /bin/bash
cat /etc/gitlab/initial_root_password



