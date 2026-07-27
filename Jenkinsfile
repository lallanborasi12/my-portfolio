pipeline {
    agent any

    environment {
        APP_DIR = "/var/www/my-portfolio"
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

        stage('Deploy') {
            steps {
                sh '''
                pm2 delete my-portfolio || true
                pm2 start npm --name my-portfolio -- start
                '''
            }
        }
    }
}
