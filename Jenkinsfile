pipeline {
    agent any

    environment {
        DO_CREDENTIALS = credentials('digitalocean-ssh')
        REMOTE_USER = 'chris'
        REMOTE_HOST = '164.92.227.136'
        PROJECT_DIR = '/var/www/portfolio'
    }

    stages {
        stage('Setup SSH') {
            steps {
                script {
                    sh "ssh-keyscan -H $REMOTE_HOST >> ~/.ssh/known_hosts"
                }
            }
        }

        stage('Connect to Server') {
            steps {
                script {
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh "ssh $REMOTE_USER@$REMOTE_HOST 'echo Connected ✔️'"
                    }
                }
            }
        }

        stage('Auto Commit Local Changes') {
            steps {
                script {
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh """
                            ssh $REMOTE_USER@$REMOTE_HOST '
                                cd $PROJECT_DIR && 
                                git config --global --add safe.directory $PROJECT_DIR &&
                                if [ -n \"\$(git status --porcelain)\" ]; then
                                    echo "⚠️ Local changes detected – committing them..." &&
                                    git add . &&
                                    git commit -m \"Auto-commit before pull (via Jenkins)\" &&
                                    git push origin main
                                else
                                    echo "✅ No local changes – continue"
                                fi
                            '
                        """
                    }
                }
            }
        }

        stage('Pull Latest Changes') {
            steps {
                script {
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh "ssh $REMOTE_USER@$REMOTE_HOST 'cd $PROJECT_DIR && git pull origin main'"
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh "ssh $REMOTE_USER@$REMOTE_HOST 'cd $PROJECT_DIR && npm install'"
                    }
                }
            }
        }

        stage('Build Project') {
            steps {
                script {
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh "ssh $REMOTE_USER@$REMOTE_HOST 'cd $PROJECT_DIR && npm run build'"
                    }
                }
            }
        }

        stage('Restart Application') {
            steps {
                script {
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh "ssh $REMOTE_USER@$REMOTE_HOST 'cd $PROJECT_DIR && pm2 restart portfolio-app'"
                    }
                }
            }
        }
    }
}
