pipeline {
    agent any
    environment {
        HOST = "13.218.38.62"
    }

    stages {
        stage('Build') {
            steps {
                echo "Building branch: ${env.BRANCH_NAME}"
            }
        }

         stage("Set Target") {
            steps {
                script {
                    if (env.BRANCH_NAME == "development") {
                        env.TARGET_DIR = "development"
                        env.APP_URL = "http://development.myagecy.com"
                        env.APP_PORT = "3000"
                        env.APP_USER = "development_user"
                    } 
                    else if (env.BRANCH_NAME == "stage") {
                        env.TARGET_DIR = "stage"
                        env.APP_URL = "http://stage.myagecy.com"
                        env.APP_PORT = "4000"
                        env.APP_USER = "stage_user"
                    } 
                    else if (env.BRANCH_NAME == "main") {
                        env.TARGET_DIR = "production"
                        env.APP_URL = "http://www.myagecy.com"
                        env.APP_PORT = "8080"
                        env.APP_USER = "master_user"
                    } else {
                        error "Unsupported branch: ${env.BRANCH_NAME}"
                    }                    
                }
            }
        }

        stage("Deploy") {
            steps{
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: "ec2-ssh-key",
                        usernameVariable: "SSH_USER",
                        keyFileVariable: "SSH_KEY"
                    )
                ]){
                    sh """
                       jq --arg url "$APP_URL" \
                            --arg port "$APP_PORT" \
                            --arg user "$APP_USER" \
                            '.url = \$url | .port = \$port | .user = \$user' \
                            server.json > updated_server.json && \
                            mv updated_server.json server.json

                        jq . server.json

                        scp -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                        "$WORKSPACE/server.json" \
                        "$SSH_USER@$HOST:/home/$SSH_USER/$TARGET_DIR"
                        echo "File copied successfully with updated code"
                    """
                }

            }
        }
    }
}
