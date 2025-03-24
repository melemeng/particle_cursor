pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'git@github.com:melemeng/particle_cursor.git'
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
                sh 'ssh -i /root/.ssh/digitalocean_jenkins_key chris@164.92.227.136"cd /var/www/portfolio && git pull origin main && npm install && npm run build && pm2 restart portfolio-app"'
            }
        }
    }
}
