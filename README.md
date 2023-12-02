# Foodgram - Продуктовый помощник
### Описание:
Foodgram это веб сервис, на котором пользователи публикуют рецепты, добавляют чужие рецепты в избранное и подписываются на публикации других авторов.

Проект состоит из следующих страниц: 
- главная,
- страница рецепта,
- страница пользователя,
- страница подписок,
- избранное,
- список покупок,
- создание и редактирование рецепта.

### Как запустить проект:
#### Запуск проекта Foodgram через Docker на сервер (целая песня):

**1. ### Подготовка образов:**
Зайти в папки infra, backend, frontend и командами
docker build -t {docker-login}/foodgram_frontend .
docker build -t {docker-login}/foodgram_backend .
docker build -t {docker-login}/foodgram_gateway .
**2. ### Пушим образы в Docker Hub:**
docker push {docker-login}/foodgram_frontend
docker push {docker-login}/foodgram_backend
docker push {docker-login}/foodgram_gateway
**3. ### Заходим на сервер устанавливаем Docker, nginx и создаем папку foodgram:**
```
ssh -i C:/Users/{windows-login}/.ssh/vm_access/{login-cloud} {root-user}@{ip}
```
Пример
```
ssh -i C:/Users/Andrey/.ssh/vm_access_2023-10-11/yc-shooroop87 yc-user@84.252.139.195
```
Обновляем и устанавливаем докер
```
sudo apt update
```
```
sudo apt-get update
```
```
sudo apt-get install vim nano
```
```
sudo apt install curl
```
```
curl -fSL https://get.docker.com -o get-docker.sh
```
```
sudo sh ./get-docker.sh
```
```
sudo apt-get install docker-compose-plugin 
```
```
sudo apt install nginx -y 
```
```
sudo nano /etc/nginx/sites-enabled/default
```
Создайте примерно такую структуру:
```
server {
    server_name xxx.xxx.xx.xxx yyyyyyy.com;
    server_tokens off;

    location / {
      proxy_set_header Host $http_host;
      proxy_pass http://127.0.0.1:8000;
    }
}
```
Пример
```
server {
    server_name 84.252.139.195 berlinweek.ru;
    server_tokens off;

    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:8000;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/berlinweek.ru/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/berlinweek.ru/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = berlinweek.ru) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    server_name 84.252.139.195 berlinweek.ru;
    listen 80;
    return 404; # managed by Certbot

}

```
Создаём папку foodgram
mkdir foodgram
**4. ### Копируем продуктинвый докер файл**
Пример
```
scp -i C:/Users/Andrey/.ssh/vm_access/yc-shooroop87 docker-compose.production.yml \
    yc-user@84.252.139.195:/home/yc-user/foodgram/docker-compose.production.yml
```
**5. ### Cоздаём .env**
POSTGRES_DB=foodgram
POSTGRES_USER=foodgram_user
POSTGRES_PASSWORD=foodgram_password
DB_NAME=foodgram
DB_HOST=db
DB_PORT=5432
DJANGO_SECRET_KEY=django-insecure-_i+y8x6!i-^6o5x()3p2$_nl5^44%(t1c69ai!&6oz#76s1c-+
DEBUG_VALUE=True
DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 84.252.139.195 berlinweek.ru

В **settings.py** должно быть так:
import os
from pathlib import Path

from dotenv import find_dotenv, load_dotenv

load_dotenv(find_dotenv())

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = str(os.getenv("DJANGO_SECRET_KEY"))
DEBUG = os.getenv('DEBUG_VALUE') == 'True'

ALLOWED_HOSTS = str(os.getenv("DJANGO_ALLOWED_HOSTS")).split(' ')

**6. ### На git в репозиторие в разделе Settings > Secrets and variables > Action Добавить следующие "секреты" по шаблону:**
```
DOCKER_USERNAME <никнейм DockerHub>
DOCKER_PASSWORD <пароль от DockerHub>
HOST <IP вашего сервера>
SSH_KEY <Ваш приватный SSH-ключ>
SSH_PASSPHRASE <Ваш пароль от сервера>
USER <имя пользователя для подключения к серверу>
TELEGRAM_TO <id вашего телеграм аккаунта>
TELEGRAM_TOKEN <токен вашего телеграм бота>
```

**7. ### На git сделать пуш чтобы запустить workflow:**
```
git add .
```
```
git commit -m '<текст коммита>'
```
```
git push
```
**8. ### Зайти в на сервер в docker backend****
```
docker container ls -a
sudo docker exec -it {id-container-backend} bash
python manage.py ingredients_import
python manage.py tags_import
```

### Автор:
**Andrey Egorov**
