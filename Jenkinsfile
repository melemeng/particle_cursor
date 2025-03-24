pipeline {
    agent any

    environment {
        DO_CREDENTIALS = credentials('digitalocean-ssh')
    }

    stages {
        stage('Setup SSH') {
            steps {
                script {
                    // Add the remote server to the known hosts
                    sh "ssh-keyscan -H 164.92.227.136 >> ~/.ssh/known_hosts"
                }
            }
        }

        stage('Connect to DigitalOcean') {
            steps {
                script {
                    // Use the SSH agent to connect and verify
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh """
                            ssh chris@164.92.227.136 'echo "Successfully connected to DigitalOcean!"'
                        """
                    }
                }
            }
        }

        stage('Pull Latest Changes') {
            steps {
                script {
                    // Pull the latest changes from the Git repository
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh """
                            ssh chris@164.92.227.136 'cd /var/www/portfolio && git pull origin main'
                        """
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install the required dependencies
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh """
                            ssh chris@164.92.227.136 'cd /var/www/portfolio && npm install'
                        """
                    }
                }
            }
        }

        stage('Build Project') {
            steps {
                script {
                    // Run the build command
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh """
                            ssh chris@164.92.227.136 'cd /var/www/portfolio && npm run build'
                        """
                    }
                }
            }
        }

        stage('Restart Application') {
            steps {
                script {
                    // Restart the application with PM2
                    sshagent (credentials: ['digitalocean-ssh']) {
                        sh """
                            ssh chris@164.92.227.136 'cd /var/www/portfolio && pm2 restart portfolio-app'
                        """
                    }
                }
            }
        }
    }
}
