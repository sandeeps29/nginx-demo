pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'nginx-demo'
        DOCKER_TAG = "${BUILD_NUMBER}"
        DEPLOY_TIME = sh(script: 'date "+%Y-%m-%d %H:%M:%S"', returnStdout: true).trim()
    }

    stages {
        stage('Prepare HTML') {
            steps {
                script {
                    // Replace variables in index.html
                    sh """
                        sed -i "s/\${BUILD_NUMBER}/${BUILD_NUMBER}/g" index.html
                        sed -i "s/\${DEPLOY_TIME}/${DEPLOY_TIME}/g" index.html
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh '''
                        docker stop nginx-demo || true
                        docker rm nginx-demo || true
                        docker run -d -p 8081:80 --name nginx-demo ${DOCKER_IMAGE}:${DOCKER_TAG}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful! Site is available at http://localhost:8080'
        }
        failure {
            echo 'Deployment failed! Check logs for details.'
        }
    }
}
