pipeline {

    agent any

    environment {
        CI = "false"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/lallanborasi12/gitaction12.git'
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
                    mkdir -p /var/www/backup

                    if [ "$(ls -A /var/www/html)" ]; then
                        tar -czf /var/www/backup/backup-$(date +%Y-%m-%d-%H-%M-%S).tar.gz \
                        -C /var/www/html .
                    fi
                '''
            }
        }


        stage('Deploy') {
            steps {
                sh '''
                   rm -rf /var/www/html/*

                    cp -r build/* /var/www/html/

                    chown -R jenkins:jenkins /var/www/html
                    chmod -R 755 /var/www/html
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

    }
}
