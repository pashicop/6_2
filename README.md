# 6_2
## 1
docker-compose.yml
```
# Use postgres/example user/password credentials
version: '3.1'

services:

  db:
    image: postgres:12
    restart: always
    ports:
      - 54320:5432
    volumes:
      - ./data/db:/var/lib/postgresql/data/
      - ./data/backup:/var/lib/postgresql/backup
    environment:
      POSTGRES_PASSWORD: example
      PGDATA: /var/lib/postgresql/data/pgdata
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```
