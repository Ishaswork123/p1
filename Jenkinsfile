pipeline {
    agent any

    environment {
        APACHE_SERVER_IP = '16.16.215.148'  // Apache server's IP
        SSH_CREDENTIAL_ID = 'apache'       // Jenkins SSH Credential ID
        DEPLOY_PATH = '/var/www/html/'     // Apache's default document root
    }

    triggers {
        // Trigger the pipeline when a GitHub push event is received
        githubPush()
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning repository..."
                    // Clone the GitHub repository
                    checkout scm
                }
            }
        }

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

                        // Copy all files in the repository to the Apache server
                        sh "scp -r * ubuntu@${APACHE_SERVER_IP}:${DEPLOY_PATH}"
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "Verifying deployment of files on remote server..."

                    // Verify each HTML file in the repository
                    sh "ls *.html | xargs -I {} curl -f http://${APACHE_SERVER_IP}/{}"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully after Git commit!'
        }
        failure {
            echo 'Deployment failed. Check the logs for more details.'
        }
    }
}
