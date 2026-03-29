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
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f docker/Dockerfile ."
            }   
        }
        stage('Login to Docker Hub') {
            steps {
                echo 'Logging into Docker Hub...'
                withCredentials([usernamePassword(
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
        stage('Update Helm values'){
            steps{
                echo'Updating Helm Values'
                sh '''
                sed -i "s/tag:.*/tag: ${IMAGE_TAG}/" /helm/welcome/values.yaml
                '''
            }
        }
        stage('Push Changes (GitOps)'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: 'git-credentials',
                    usernameVariable: 'GIT_USERNAME',
                    passwordVariable: 'GIT_PASSWORD'
                )]){
                    sh '''
                        git config user.email "temgvan@gmail.com"
                        git config user.name "bcho77"

                        git add helm/welcome/values.yaml
                        git commit -m "Update image tag to ${IMAGE_TAG}"
                        git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/bcho77/devops-cicd-project.git HEAD:main
                    '''
                    }
                }
            }

    }
}