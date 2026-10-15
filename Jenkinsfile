pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'suresh45808/jenkins-docker-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                sh 'docker tag $DOCKER_IMAGE:$BUILD_NUMBER $DOCKER_IMAGE:latest'
            }
        }

        stage('Test Image') {
            steps {
                sh 'docker image inspect $DOCKER_IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'Docker Credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    sh '''
                        set +x
                        echo "$DOCKER_TOKEN" |
                            docker login --username "$DOCKER_USER" \
                            --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                sh 'docker push $DOCKER_IMAGE:latest'
            }
        }
        stage('Pull and run the image'){
            steps{
                sh 'docker rm -f DockerCheckProd'
                sh 'docker run -d --name DockerCheckProd -p 80:80 suresh45808/jenkins-docker-demo'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
