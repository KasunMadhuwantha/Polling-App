pipeline {
    agent any
    environment {
        SECRET_KEY = credentials('jenkins_secret_key')
        DB_ENGINE = 'django.db.backends.sqlite3'
        ALLOWED_HOSTS = '127.0.0.1'
        DB_NAME = 'db.sqlite3'
        DB_USER = 'pollingdb'
        DB_PASSWORD = 'password2020'
        DB_HOST = 'app-server'
        DB_PORT = '5432'
        TIME_ZONE = 'GMT'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    python manage.py test
                '''
            }
        }

        stage('Develop') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no deployment-user@192.168.56.108 "source venv/bin/activate; \
                cd polling; \
                git pull origin master; \
                pip install -r requirements.txt; \
                python manage.py migrate; \
                sudo systemctl restart nginx; \
                sudo systemctl restart gunicorn"'
            }
        }
    }
}
