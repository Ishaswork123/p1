pipeline {
    agent any

    environment {
        APACHE_SERVER_IP = '13.60.60.152'  // Apache server's IP
        SSH_CREDENTIAL_ID = 'apache'       // Jenkins SSH Credential ID
        DEPLOY_PATH = '/var/www/html/'     // Apache's default document root
        LOCAL_FILE = 'Hello.html'          // The file to deploy
    }

    stages {
        stage('Setup SSH Known Hosts') {
            steps {
                sshagent(["${SSH_CREDENTIAL_ID}"]) {
                    script {
                        echo "Adding ${APACHE_SERVER_IP} to known_hosts..."
                        sh "ssh-keyscan -H ${APACHE_SERVER_IP} >> ~/.ssh/known_hosts"
                    }
                }
            }
        }

        stage('Deploy HTML to Apache Server') {
            steps {
                sshagent(["${SSH_CREDENTIAL_ID}"]) {
                    script {
                        echo "Deploying ${LOCAL_FILE} to ${APACHE_SERVER_IP}..."

                        // Copy the file to the Apache server's document root
                        sh "scp ${LOCAL_FILE} ubuntu@${APACHE_SERVER_IP}:${DEPLOY_PATH}${LOCAL_FILE}"
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "Verifying deployment of ${LOCAL_FILE} on remote server..."

                    // Verify the file is served correctly
                    sh "curl http://${APACHE_SERVER_IP}/${LOCAL_FILE}"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Check the logs for more details.'
        }
    }
}
