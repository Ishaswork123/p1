pipeline {
    agent any

    environment {
        APACHE_SERVER_IP = '13.60.60.152'  // Replace with your Apache server's IP
        SSH_CREDENTIAL_ID = 'apache'  // Replace with your Jenkins SSH Credential ID
        DEPLOY_PATH = '/var/www/html/'  // Apache's default document root
        LOCAL_FILE = 'Hello.html'  // The file to deploy
    }

    stages {
        stage('Deploy HTML to Apache Server') {
            steps {
                sshagent([apache]) {
                    script {
                        echo "Deploying ${'Hello.html'} to remote Apache server..."

                        // Add the server's SSH key to known_hosts
                        sh "ssh-keyscan -H ${13.60.60.152} >> ~/.ssh/known_hosts"

                        // Copy the provided file to the Apache server's document root
                        sh "scp ${LOCAL_FILE} ubuntu@${13.60.60.152}:${'Hello.html'}"
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "Verifying ${'Hello.html'} deployment on remote server..."

                    // Verify the file is served correctly
                    sh "curl http://${13.60.60.152}/${'Hello.html'}"
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

