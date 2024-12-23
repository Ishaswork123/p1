pipeline {
    agent any

    environment {
        APACHE_SERVER_IP = '51.20.255.188'  // Replace with your Apache server's IP
        SSH_CREDENTIAL_ID = 'apache'  // Replace with your Jenkins SSH Credential ID
        DEPLOY_PATH = '/var/www/html/'  // Apache's default document root
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Checkout the code from GitHub
                git branch: 'main', url: 'https://github.com/Ishaswork123/p1.git'
            }
        }

        stage('Deploy to Apache Server') {
            steps {
                // Use SSH to copy the file to the Apache server
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'Apache Server',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'index.html',
                                    remoteDirectory: "${DEPLOY_PATH}"
                                )
                            ],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: true
                        )
                    ]
                )
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
