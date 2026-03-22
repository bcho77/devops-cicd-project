pipeline {
    agent any

    environment{
        IMAGE_NAME = 'vaninoel/welcome-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning repository.....'
                git branch: 'main', url: 'https://github.com/bcho77/devops-cicd-project.git'
            }
        }
        stage('Build docker image') {
            steps {
                echo 'Building docker image...'
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f docker/dockerfile ."
            }
        }
        stage('Login to Docker Hub') {
            steps {
                echo 'Logging into Docker Hub...'
                withCredentials([usernamPassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]){
                    sh '''
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    '''
                }
            }
        }
        stage('Push to Docker Hub'){
            steps{
                echo 'Pushing to Docker Hub'
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"

            }
        }
    }
}