pipeline {
    agent any

    environment {
        APP_NAME   = "my-portfolio"
        DEPLOY_DIR = "/var/www/html/my-portfolio"
        BACKUP_DIR = "/var/www/backup"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-ssh',
                    url: 'git@github.com:lallanborasi12/my-portfolio.git'
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

        stage('Backup') {
            steps {
                sh '''
                sudo mkdir -p ${BACKUP_DIR}

                if [ -d "${DEPLOY_DIR}" ]; then
                    TIMESTAMP=$(date +%F-%H-%M-%S)
                    sudo cp -r ${DEPLOY_DIR} ${BACKUP_DIR}/${APP_NAME}-${TIMESTAMP}
                fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                sudo rm -rf ${DEPLOY_DIR}
                sudo mkdir -p ${DEPLOY_DIR}

                sudo rsync -av --delete \
                    --exclude='.git' \
                    --exclude='Jenkinsfile' \
                    ./ ${DEPLOY_DIR}/

                cd ${DEPLOY_DIR}

                npm install --production

                pm2 delete ${APP_NAME} || true
                pm2 start npm \
                    --name ${APP_NAME} \
                    --cwd ${DEPLOY_DIR} \
                    -- start

                pm2 save
                '''
            }
        }
    }
}
