Cloud storage
===============

Приложение "облачное хранилище". Приложение позволяет пользователям загружать, отправлять, скачивать файлы.

***Содержание:***

- [Технологии](#технологии)
- [Начало работы](#начало-работы)
  - [Настройка сервера](#настройка-сервера)
  - [Настройка проекта](#настройка-проекта)
    - [Настройка БД](#настройка-бд)
    - [Клонирование проекта](#клонирование-проекта)
    - [Настройка переменных окружения backend](#env-backend)
    - [Настройка переменных окружения frontend](#env-frontend)
  - [Запуск проекта через Docker Compose](#run-docker)
- [To do](#to-do)


# Технологии <a name="технологии"></a>

<style>
    .tech-logo-l {
        height: 50px;
    }

    .tech-logo-s {
        height: 25px;
    }

    .tech-logo {
        border: 2px solid #000;
        border-radius: 8px;
        padding: 5px;
        margin: 0 10px;
        background: white;
        transition: transform 0.3s ease, box-shadow 0.3s ease;
    }

    .tech-logo:hover {
        transform: scale(1.05);
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);

    .tailwind-logo {
        height: 25px !important;
    }
</style>

### Backend

<img src="./assets/img/Django_logo.png" alt="Django" title="Django" class="tech-logo tech-logo-l">
<img src="https://www.postgresql.org/media/img/about/press/elephant.png" alt="PostgreSQL" title="PostgreSQL" class="tech-logo tech-logo-l">

### Frontend

<img src="./assets/img/TypeScript_logo.png" alt="TypeScript" title="TypeScript" class="tech-logo tech-logo-l">
<img src="https://vitejs.dev/logo-with-shadow.png" alt="Vite" title="Vite" class="tech-logo tech-logo-l">
<img src="./assets/img/React_logo_light.svg" alt="React" title="React" class="tech-logo tech-logo-l">
<img src="./assets/img/Tailwind_CSS_logo.png" alt="Tailwind" title="Tailwind" class="tech-logo tech-logo-s">
<img src="./assets/img/Redux_Logo.png" alt="Redux Toolkit" title="Redux Toolkit" class="tech-logo tech-logo-s">
<img src="./assets/img/react-router-color.svg" alt="React Router" title="React Router" class="tech-logo tech-logo-s" >


# Начало работы <a name="начало-работы"></a>

### Настройка сервера <a name="настройка-сервера"></a>
1. Подключитесь к серверу через root пользователя
    ```bash
    ssh <ip>
    ```
    где `<ip>` - ip-адрес сервера, например:
    ```bash
    ssh 109.71.245.96
    ```

1. Установите postgresql
    ```bash
    apt install postgresql
    ```

1. Установите Docker

    https://docs.docker.com/engine/install/ubuntu/

1. Создайте пользователя и дайте ему права админа
    ```bash
    adduser <user>
    usermod <user> -aG sudo
    ```
    где `<user>` - имя пользователя

1. Переключитесь на созданного пользователя
    ```bash
    su - <user>
    ```

1. Создайте каталог проекта и переназначте владельца каталога
    ```bash
    sudo mkdir -p /var/www/cloud_storage
    sudo chown <user>:<user> -R /var/www/cloud_storage/
    ```

### Настройка проекта <a name="настройка-проекта"></a>
#### <ins>Настройка БД</ins> <a name="настройка-бд"></a>

Настройте БД в соотвтествии с [инструкцией](https://github.com/Unker/cloud_storage_server/tree/main?tab=readme-ov-file#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B1%D0%B4)

#### <ins>Клонирование проекта</ins> <a name="клонирование-проекта"></a>

1. Переключитесь на созданного ранее пользователя, если еще не
    ```bash
    su - <user>
    ```

1. Перейдите в каталог проекта и склонируйте его с репозитория
    ```bash
    cd /var/www/cloud_storage
    git clone https://github.com/Unker/cloud_storage_deploy.git .
    cd cloud_storage_deploy
    ```

1. Скачайте субмодули
    ```bash
    git submodule update --init --recursive
    ```

#### <ins>Настройка переменных окружения backend</ins> <a name="env-backend"></a>
1. Сгенерируйте SECRET_KEY для Django:
   ```bash
   python3 manage.py shell
   from django.core.management import utils
   utils.get_random_secret_key()
   exit()
   ```

1. Создайте .env файл и откройте на редактирование:
   ```bash
   touch ./cloud_storage_server/cloud_storage/.env
   nano ./cloud_storage_server/cloud_storage/.env
   ```
   

1. Заполните .env следующим содержимым:
   ```
   SECRET_KEY_DJANGO='django-insecure-xxx'
   CORS_ALLOWED_HOSTS='http://localhost:3000,http://109.71.245.96:3000'
   ALLOWED_HOSTS='localhost,0.0.0.0,109.71.245.96'
   DB_NAME=cloud_storage_db
   DB_HOST=109.71.245.96
   DB_PORT=5432
   DB_USER=<имя_суперпользователя>
   DB_PASSWORD=<пароль_суперпользователя>
   STORAGE_PATH=users_files
   ```
    - Вместо `xxx` в `SECRET_KEY_DJANGO` вставьте ключ, полученный в предыдущем действии.
    - Для полей `CORS_ALLOWED_HOSTS` и `ALLOWED_HOSTS` вместо `109.71.245.96` укажите адреса вашего сервера
    - Для поля `DB_HOST` - текущий адрес сервера
    - Для `DB_USER` - имя вашего суперпользователя
    - Для `DB_PASSWORD` - пароль вашего суперпользователя

#### <ins>Настройка переменных окружения frontend</ins> <a name="env-frontend"></a>
1. Создайте .env файл и откройте на редактирование:
    ```bash
    touch ./cloud_storage_client/.env
    nano ./cloud_storage_client/.env
    ```

1. Заполните .env следующим содержимым:
    ```
    VITE_SERVER_URL='http://109.71.245.96/api'
    ```
***Вместо `109.71.245.96` укажите адреса вашего сервера***

## Запуск проекта через Docker Compose <a name="run-docker"></a>
```bash
cd /var/www/cloud_storage/cloud_storage_deploy
docker compose up --build
```

## Todo <a name="to-do"></a>

Подключить доменное имя