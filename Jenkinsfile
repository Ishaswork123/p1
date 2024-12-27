pipeline {
    agent any

    environment {
        APACHE_SERVER_IP = '16.16.215.148'  // Apache server's IP
        SSH_CREDENTIAL_ID = 'apache'       // Jenkins SSH Credential ID
        DEPLOY_PATH = '/var/www/html/'     // Apache's default document root
        LOCAL_FILES = 'Hello.html home.html addBook.html' // Files to deploy
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

        stage('Deploy HTML Files to Apache Server') {
            steps {
                sshagent(["${SSH_CREDENTIAL_ID}"]) {
                    script {
                        echo "Deploying files to ${APACHE_SERVER_IP}..."

                        // Copy all files to the Apache server's document root
                        sh "scp ${LOCAL_FILES} ubuntu@${APACHE_SERVER_IP}:${DEPLOY_PATH}"
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "Verifying deployment of files on remote server..."

                    // Verify each file is served correctly
                    LOCAL_FILES.split(' ').each { file ->
                        echo "Verifying ${file}..."
                        sh "curl http://${APACHE_SERVER_IP}/${file}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment of all files completed successfully!'
        }
        failure {
            echo 'Deployment failed. Check the logs for more details.'
        }
    }
}
