pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jack1503/my-portfolio"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "my-app-container"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$IMAGE_TAG'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d -p 80:80 --name $CONTAINER_NAME $DOCKER_IMAGE:$IMAGE_TAG
                '''
            }
        }
    }
}
