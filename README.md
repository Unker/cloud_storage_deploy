Cloud Storage
===============

The "Cloud Storage" application allows users to upload, send, and download files.

***Contents:***

- [Technologies](#technologies)
- [Getting Started](#getting-started)
  - [Server Setup](#server-setup)
  - [Project Setup](#project-setup)
    - [Database Setup](#database-setup)
    - [Cloning the Project](#cloning-the-project)
    - [Setting Backend Environment Variables](#env-backend)
    - [Setting Frontend Environment Variables](#env-frontend)
  - [Running the Project with Docker Compose](#run-with-docker)
- [To Do](#to-do)

# Technologies <a name="technologies"></a>

### Backend
<img src="./assets/img/Django_logo.png" alt="Django" title="Django" height="50" style="margin: 10px;">
<img src="https://www.postgresql.org/media/img/about/press/elephant.png" alt="PostgreSQL" title="PostgreSQL" height="50" style="margin: 10px;">
<img src="https://nginx.org/nginx.png" alt="nginx" title="nginx" height="50" style="margin: 10px;">
<img src="https://gunicorn.org/images/logo.jpg" alt="gunicorn" title="gunicorn" height="50" style="margin: 10px;">



### Frontend
<img src="./assets/img/TypeScript_logo.png" alt="TypeScript" title="TypeScript" height="50" style="margin: 10px;">
<img src="https://vitejs.dev/logo-with-shadow.png" alt="Vite" title="Vite" height="50" style="margin: 10px;">
<img src="./assets/img/React_logo_light.svg" alt="React" title="React" height="50" style="margin: 10px;">
<img src="./assets/img/Tailwind_CSS_logo.png" alt="Tailwind" title="Tailwind" height="25" style="margin: 10px;">

<img src="./assets/img/Redux_Logo.png" alt="Redux Toolkit" title="Redux Toolkit" height="25" style="margin: 10px;">
<img src="./assets/img/react-router-color.svg" alt="React Router" title="React Router" height="25" style="margin: 10px;">

# Getting Started <a name="getting-started"></a>

### Server Setup <a name="server-setup"></a>
1. Connect to the server as the root user:
    ```bash
    ssh <ip>
    ```
    where `<ip>` is the server's IP address, for example:
    ```bash
    ssh 109.71.245.96
    ```

1. Install PostgreSQL:
    ```bash
    apt install postgresql
    ```

1. Install Docker:

    https://docs.docker.com/engine/install/ubuntu/

1. Create a user and grant them admin privileges:
    ```bash
    adduser <user>
    usermod <user> -aG sudo
    ```
    where `<user>` is the username.

1. Switch to the newly created user:
    ```bash
    su - <user>
    ```

1. Create the project directory and reassign ownership:
    ```bash
    sudo mkdir -p /var/www/cloud_storage
    sudo chown <user>:<user> -R /var/www/cloud_storage/
    ```

### Project Setup <a name="project-setup"></a>

#### <ins>Database Setup</ins> <a name="database-setup"></a>

Set up the database according to the [instructions](https://github.com/Unker/cloud_storage_server/tree/main?tab=readme-ov-file#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B1%D0%B4).

#### <ins>Cloning the Project</ins> <a name="cloning-the-project"></a>

1. Switch to the previously created user, if not already done:
    ```bash
    su - <user>
    ```

1. Navigate to the project directory and clone it from the repository:
    ```bash
    cd /var/www/cloud_storage
    git clone https://github.com/Unker/cloud_storage_deploy.git .
    cd cloud_storage_deploy
    ```

1. Download the submodules:
    ```bash
    git submodule update --init --recursive
    ```

#### <ins>Setting Backend Environment Variables</ins> <a name="env-backend"></a>
1. Generate a SECRET_KEY for Django:
   ```bash
   python3 manage.py shell
   from django.core.management import utils
   utils.get_random_secret_key()
   exit()
   ```

1. Create a `.env` file and open it for editing:
   ```bash
   touch ./cloud_storage_server/cloud_storage/.env
   nano ./cloud_storage_server/cloud_storage/.env
   ```

1. Populate the `.env` file with the following content:
   ```
   SECRET_KEY_DJANGO='django-insecure-xxx'
   CORS_ALLOWED_HOSTS='http://localhost:3000,http://109.71.245.96:3000'
   ALLOWED_HOSTS='localhost,0.0.0.0,109.71.245.96'
   DB_NAME=cloud_storage_db
   DB_HOST=109.71.245.96
   DB_PORT=5432
   DB_USER=<superuser_name>
   DB_PASSWORD=<superuser_password>
   STORAGE_PATH=users_files
   ```
    - Replace `xxx` in `SECRET_KEY_DJANGO` with the key created in the previous step.
    - For `CORS_ALLOWED_HOSTS` and `ALLOWED_HOSTS`, replace `109.71.245.96` with your server's address.
    - For `DB_HOST`, use the current server address.
    - For `DB_USER`, use your superuser name.
    - For `DB_PASSWORD`, use your superuser password.

#### <ins>Setting Frontend Environment Variables</ins> <a name="env-frontend"></a>
1. Create a `.env` file and open it for editing:
    ```bash
    touch ./cloud_storage_client/.env
    nano ./cloud_storage_client/.env
    ```

1. Populate the `.env` file with the following content:
    ```
    VITE_SERVER_URL='http://109.71.245.96/api'
    ```
***Replace `109.71.245.96` with your server's address.***

## Running the Project with Docker Compose <a name="run-with-docker"></a>
```bash
cd /var/www/cloud_storage/cloud_storage_deploy
docker compose up --build
```

## To Do <a name="to-do"></a>

+ Connect a domain name.
+ Set up CI/CD.