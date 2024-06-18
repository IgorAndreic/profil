# Развертывание на сервере

This project is a full-stack web application built with Django, PostgreSQL, React, and Yarn. It provides a comprehensive guide on how to set up the development environment, install dependencies, and deploy the project using Nginx and Gunicorn.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [Setting Up the Database](#setting-up-the-database)
4. [Running the Project](#running-the-project)
5. [Deployment](#deployment)

## Prerequisites

Before you begin, ensure you have the following installed:

- Python 3.x
- Node.js and Yarn
- PostgreSQL
- Nginx
- Git

## Installation

### Backend (Django)

1. Clone the repository:
    ```bash
    git clone https://github.com/yourusername/your-repo.git
    cd your-repo
    ```

2. Create a virtual environment and activate it:
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

3. Install Django dependencies:
    ```bash
    pip install -r requirements.txt
    ```

### Frontend (React)

1. Navigate to the frontend directory:
    ```bash
    cd frontend
    ```

2. Install React dependencies using Yarn:
    ```bash
    yarn install
    ```

## Setting Up the Database

1. Ensure PostgreSQL is running and create a new database:
    ```bash
    createdb your_database_name
    ```

2. Update the `settings.py` file in the Django project to include your database settings:
    ```python
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'your_database_name',
            'USER': 'your_username',
            'PASSWORD': 'your_password',
            'HOST': 'localhost',
            'PORT': '5432',
        }
    }
    ```

3. Apply migrations:
    ```bash
    python manage.py migrate
    ```

## Running the Project

### Backend

1. Start the Django development server:
    ```bash
    python manage.py runserver
    ```

### Frontend

1. Start the React development server:
    ```bash
    yarn start
    ```

## Deployment

### Setting Up Nginx

1. Install Nginx if it's not already installed:
    ```bash
    sudo apt-get install nginx
    ```

2. Create an Nginx configuration file for your project:
    ```bash
    sudo nano /etc/nginx/sites-available/your_project
    ```

3. Add the following configuration:
    ```nginx
    server {
        listen 80;
        server_name your_domain_or_ip;

        location / {
            proxy_pass http://127.0.0.1:3000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        location /api/ {
            proxy_pass http://127.0.0.1:8000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    ```

4. Create a symbolic link to enable the configuration:
    ```bash
    sudo ln -s /etc/nginx/sites-available/your_project /etc/nginx/sites-enabled
    ```

5. Test the Nginx configuration:
    ```bash
    sudo nginx -t
    ```

6. Restart Nginx:
    ```bash
    sudo systemctl restart nginx
    ```

### Setting Up Gunicorn

1. Install Gunicorn:
    ```bash
    pip install gunicorn
    ```

2. Create a Gunicorn systemd service file:
    ```bash
    sudo nano /etc/systemd/system/gunicorn.service
    ```

3. Add the following configuration:
    ```ini
    [Unit]
    Description=gunicorn daemon
    After=network.target

    [Service]
    User=your_username
    Group=www-data
    WorkingDirectory=/path/to/your-repo
    ExecStart=/path/to/your-repo/venv/bin/gunicorn --access-logfile - --workers 3 --bind unix:/path/to/your-repo/your_project.sock your_project.wsgi:application

    [Install]
    WantedBy=multi-user.target
    ```

4. Start and enable the Gunicorn service:
    ```bash
    sudo systemctl start gunicorn
    sudo systemctl enable gunicorn
    ```

### Building the React App

1. Build the React app for production:
    ```bash
    yarn build
    ```

2. Move the build files to the Nginx web root:
    ```bash
    sudo cp -r build/* /var/www/html
    ```

Now your project should be up and running on your server. Access it via your domain or IP address.
