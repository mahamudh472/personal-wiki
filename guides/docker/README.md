# Docker 

### Basics 

| Layer                  | What it represents                                 | Example in Django world                            |
| ---------------------- | -------------------------------------------------- | -------------------------------------------------- |
| **Image**              | Blueprint (frozen snapshot of code + dependencies) | “A prebuilt kitchen” with Python, Django, gunicorn |
| **Container**          | A running instance of that image                   | “A live kitchen cooking your app right now”        |
| **Dockerfile**         | Recipe that builds an image                        | Steps to create that “kitchen”                     |
| **docker-compose.yml** | Orchestrator for multiple containers               | “Kitchen + database + Nginx, all wired together”   |


```
myproject/
 ├── Dockerfile
 ├── docker-compose.yml
 ├── requirements.txt
 ├── app/
 │   ├── manage.py
 │   └── myproject/
 │       └── settings.py
```

Docker flow:
`Dockerfile  →  docker build  →  Image  →  docker run  →  Container
`

### compose file example `docker-compose.yml`

```yaml
version: '3'
services:
  web:
    build: .
    command: gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
    volumes:
      - .:/app
    expose:
      - "8000"
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: django
      POSTGRES_PASSWORD: django
      POSTGRES_DB: django_db
    volumes:
      - postgres_data:/var/lib/postgresql/data

  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - web

volumes:
  postgres_data:
```


### Necessary commands

- Build image: `docker build -t my-django-app .`
- Run container: `docker run -d -p 8000:8000 my-django-app` (Maps container port 8000 → your host port 8000)(-d runs in background)
- List running containers: `docker ps`
- List all containers: `docker ps -a`
- Stop container: `docker stop <container_id>`
- View container logs: `docker logs <container_id>`
- Restart container: `docker restart <container_id>`
- Remove container: `docker rm <container_id>`
- Remove image: `docker rmi <image_id>`
- Access container shell: `docker exec -it <container_id> /bin/bash`
- Remove stopped containers: `docker container prune`
- Remove unused images: `docker image prune`
- Remove unused volumes: `docker volume prune`
- Assign a name to a container: `docker run --name my_container_name -d -p 8000:8000 my-django-app`
- View Docker system info: `docker system info`
- View Docker disk usage: `docker system df`
- View detailed Docker system usage: `docker system df -v`
- Docker Compose logs: `docker compose logs` or `docker compose logs -f` (follow logs in real-time)
- Using docker-compose: `docker compose up --build`
- Stop docker-compose services: `docker compose down`
- Start stopped services: `docker compose start`
- Stop running services: `docker compose stop`
