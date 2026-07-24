pipeline {
    agent any

    environment {
        WEBSITE_DIR = "/var/www/html"
        BACKUP_DIR  = "/var/www/backup"
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
        sh '''
        rm -rf .next
        npm install
        npm run build
        '''
    }
}

        stage('Verify Export') {
            steps {
                sh '''
                if [ ! -d "out" ]; then
                    echo "ERROR: out directory not found."
                    echo "Please make sure next.config.js contains:"
                    echo "output: 'export'"
                    exit 1
                fi
                '''
            }
        }

        stage('Backup Current Website') {
            steps {
                sh '''
                mkdir -p ${BACKUP_DIR}

                if [ -d "${WEBSITE_DIR}" ] && [ "$(ls -A ${WEBSITE_DIR} 2>/dev/null)" ]; then
                    tar -czf ${BACKUP_DIR}/backup_$(date +%Y%m%d_%H%M%S).tar.gz ${WEBSITE_DIR}
                    echo "Backup created successfully."
                else
                    echo "No existing website found. Skipping backup."
                fi
                '''
            }
        }

        stage('Deploy Website') {
            steps {
                sh '''
                sudo rm -rf ${WEBSITE_DIR}/*
                sudo cp -r out/* ${WEBSITE_DIR}/
                sudo chown -R www-data:www-data ${WEBSITE_DIR}
                sudo chmod -R 755 ${WEBSITE_DIR}
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
                sleep 5
                curl -I http://localhost
                '''
            }
        }
    }

    post {
        success {
            echo "===================================="
            echo "Deployment Successful"
            echo "===================================="
        }

        failure {
            echo "===================================="
            echo "Deployment Failed"
            echo "===================================="
        }
    }
}
