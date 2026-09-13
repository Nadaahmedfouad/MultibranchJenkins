pipeline {
    agent any

    environment {
        APP_NAME = 'node-app-nti'
        REPO_URL = 'https://github.com/YOUR_USERNAME/YOUR_REPO.git'
    }

    stages {

        stage('Getting Repo Files') {
            steps {
                git branch: "${BRANCH_NAME}",
                    credentialsId: 'jenkins',
                    url: "${REPO_URL}"
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
                            credentialsId: 'docker',
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