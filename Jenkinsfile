pipeline {
    agent any

    environment {
        // REPLACE THIS WITH YOUR USERNAME!
        DOCKERHUB_USER = 'prashantbhopale67'
        APP_NAME = 'k8s-demo'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build Image') {
            steps {
                script {
                    echo 'Building Docker Image...'
                    sh "docker build -t ${DOCKERHUB_USER}/${APP_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    echo 'Pushing to Docker Hub...'
                    // We need your Docker Hub login saved in Jenkins as 'dockerhub-creds'
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push ${DOCKERHUB_USER}/${APP_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                script {
                    echo 'Deploying to Cluster...'
                    // Update the YAML file with the new image tag and your username
                    sh "sed -i 's/DOCKER_USER/${DOCKERHUB_USER}/g' deployment.yaml"
                    sh "sed -i 's/\${BUILD_NUMBER}/${IMAGE_TAG}/g' deployment.yaml"
                    
                    // Apply it
                    sh "kubectl apply -f deployment.yaml"
                }
            }
        }
    }
}