# Docker configuration for Laravel
Docker environment base setup for Laravel framework

# Supported versions
- Laravel 7.x
- Laravel 8.x
- Laravel 9.x
- Laravel 10.x

# Recommendation
## When creating new Laravel projects
Before you start setting-up Docker env using this guide, here is a more recommended Docker setup for the latest Laravel version:
https://laravel.com/docs/10.x#laravel-and-docker

This out-of-the-box Laravel x Docker, helps you build a Laravel project with Laravel Sail within a Docker env.

## When dockerizing existing projects
For existing Laravel projects, we recommend to proceed and follow the **Setup Manual**.

# Setup Manual
### 1. Download Zip and extract the files

![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/6b7caac7-6f20-40f7-8eb5-dac9d8d0d262)

### 2. Copy and paste the extracted files to your existing project
- Make sure to copy only the Docker-related files

![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/09a6ffe9-3b60-45aa-a200-e7bfd0b25906)

- Paste Docker files to your Git project repository

![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/4cd8bfd8-de31-45ec-9968-786ce1264c9a)

### 3. Install or move existing Laravel project inside `src` folder

You can either install a new project or move an existing Laravel project inside `src` folder

- Install Laravel inside a new `src` folder using Composer

Documentation: https://laravel.com/docs/10.x#your-first-laravel-project

```
cd {project} && composer create-project laravel/laravel src
```

![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/78898bd6-be7e-4586-aea6-94c8ac7daeb0)

After this step, `src` folder should look like this:

![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/fa3e609c-cd14-4a85-816c-f9a4b770ed36)

### 4. Build the Docker environment

- Change directory to root project directory, then copy .env.docker to .env file

```
$ cp .env.docker .env
```

- Open `.env` and set the DB name and password:

```
# Env credentials
MYSQL_ROOT_PASSWORD={DB password here}
MYSQL_DATABASE={DB name here}
```

- Execute Docker commands

```
$ docker-compose build
$ docker-compose up -d
```

![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/aa236145-5474-403b-9784-e38a5b8f9c52)

Notes: For MacOS Apple silicon, add `platform` config for Mysql, PHPmyadmin, and Mailhog

**docker-compose.override.yml**
```
mysql:
  platform: linux/arm64/v8                   -----------> Add for Mac Apple Silicon
  image: mysql:8.0
  volumes:
    - ./docker/mysql/my.cnf:/etc/mysql/my.cnf
    - mysql:/var/lib/mysql
  restart: unless-stopped
  tty: true
  ports:
    - ${MYSQL_PORT}:3306
  environment:
    - MYSQL_DATABASE=${MYSQL_DATABASE}
    - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
    - SERVICE_NAME=mysql
    - SERVICE_TAGS=dev
  networks:
    - network
phpmyadmin:
  platform: linux/amd64                      -----------> Add for Mac Apple Silicon
  image: phpmyadmin/phpmyadmin
  environment:
    - PMA_ARBITRARY=1
    - PMA_HOST=mysql
    - PMA_USER=root
    - PMA_PASSWORD=${MYSQL_ROOT_PASSWORD}
  ports:
    - ${PHPMYADMIN_PORT}:80
  volumes:
    - ./docker/phpmyadmin/sessions:/sessions
  networks:
    - network
mail:
  platform: linux/amd64                      -----------> Add for Mac Apple Silicon
  image: mailhog/mailhog
  ports:
    - ${MAILHOG_PORT}:8025
  networks:
    - network
```

## Accessibility

You now have a Laravel x Docker project

### Web - http://localhost:9090
![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/d925c56a-3c2a-471d-87ba-f037b6b906e5)

### Mailhog - http://localhost:8025
![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/14623173-97e5-4548-b49e-aa54528a91b2)

### Database using Workbench
```
Connection Method: Standard (TCP/IP)
Hostname: 127.0.0.1
Port: 4406 (or based on your MySql port in docker-compose.yml)
Username: root
Password: {Your DB password}
```

![image](https://github.com/lorimay21/docker-base-setup/assets/28289048/7236a31c-8db4-4bc7-a1c1-3b50e432f503)

