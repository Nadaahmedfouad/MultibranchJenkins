pipeline {
    agent any

    environment {
        APP_NAME = 'node-app-nti'
    }

    stages {

        stage('Getting Repo Files') {
            steps {
                checkout scm
            }
        }

        stage('Show Current Branch') {
            steps {
                echo "Current branch is: ${BRANCH_NAME}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${APP_NAME}:${BRANCH_NAME}-${BUILD_NUMBER} .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            
                            usernameVariable: 'DOCKER_USERNAME',
                            passwordVariable: 'DOCKER_PASSWORD'
                        )
                    ]) {
                        sh """
                            echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin

                            docker tag ${APP_NAME}:${BRANCH_NAME}-${BUILD_NUMBER} \
                            ${DOCKER_USERNAME}/${APP_NAME}:${BRANCH_NAME}-${BUILD_NUMBER}

                            docker push ${DOCKER_USERNAME}/${APP_NAME}:${BRANCH_NAME}-${BUILD_NUMBER}
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline succeeded for branch: ${BRANCH_NAME}"
        }

        failure {
            echo "Pipeline failed for branch: ${BRANCH_NAME}"
        }
    }
}
