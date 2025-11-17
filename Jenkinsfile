pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        APP_NAME = "register-app-pipeline"
        DOCKER_USER = "aniljagari"
    }

    stages {

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/AnilJagari/gitops-register-app.git'
            }
        }

        stage("Update Deployment YAML with New Image Tag") {
            steps {
                sh '''
                    echo "Before update:"
                    cat deployment.yaml

                    sed -i "s|image: .*|image: ${DOCKER_USER}/${APP_NAME}:${IMAGE_TAG}|g" deployment.yaml


                    echo "After update:"
                    cat deployment.yaml
                '''
            }
        }

        stage("Push Updated Manifest to GitHub") {
            steps {

                sh '''
                    git config user.name "AnilJagari"
                    git config user.email "aniljagari111@gmail.com"
                '''

                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {

                    sh '''
                        git add deployment.yaml || true
                        git commit -m "Updated Deployment Manifest" || true

                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/AnilJagari/gitops-register-app.git main
                    '''
                }
            }
        }
    }
}
