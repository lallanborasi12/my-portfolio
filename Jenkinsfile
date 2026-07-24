pipeline {
    agent any

    environment {
        APP_NAME = 'my-portfolio'
        APP_PORT = '3000'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/lallanborasi12/my-portfolio.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Backup Current Website') {
            steps {
                sh '''
                BACKUP_DIR=/var/www/backup
                TIMESTAMP=$(date +%Y%m%d_%H%M%S)

                mkdir -p $BACKUP_DIR

                if [ -d "/var/www/html" ] && [ "$(ls -A /var/www/html 2>/dev/null)" ]; then
                    tar -czf $BACKUP_DIR/website_$TIMESTAMP.tar.gz -C /var/www html
                    echo "Backup created: $BACKUP_DIR/website_$TIMESTAMP.tar.gz"
                else
                    echo "No existing website found. Skipping backup."
                fi
                '''
            }
        }

        stage('Stop Old Application') {
            steps {
                sh '''
                pm2 delete ${APP_NAME} || true
                '''
            }
        }

        stage('Start Application') {
            steps {
                sh '''
                pm2 start npm --name ${APP_NAME} -- start
                pm2 save
                '''
            }
        }

        stage('Restart Apache') {
            steps {
                sh '''
                sudo systemctl restart apache2
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 10
                curl -I http://localhost:${APP_PORT}
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful.'
        }

        failure {
            echo 'Deployment Failed.'
        }
    }
}
