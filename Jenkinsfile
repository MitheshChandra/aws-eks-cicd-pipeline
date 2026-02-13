pipeline {
    agent any

    environment {
        // Your Docker Hub Credentials ID from Jenkins
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        
        // YOUR Docker Hub Username 
        DOCKER_IMAGE = "kmithesh/voting-app" 
        
        // K8s Context Name (We set this earlier)
        K8S_CONTEXT = "Net-pole-demo"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the image using the build number as a tag
                    sh "docker build -t $DOCKER_IMAGE:${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push $DOCKER_IMAGE:${env.BUILD_NUMBER}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Switch to the correct context
                    sh "kubectl config use-context $K8S_CONTEXT"
                    
                    // Update the deployment with the new image
                    // ASSUMPTION: You have a deployment named 'voting-app'
                    sh "kubectl set image deployment/voting-app voting-app=$DOCKER_IMAGE:${env.BUILD_NUMBER}"
                    
                    // Verify rollout status
                    sh "kubectl rollout status deployment/voting-app"
                }
            }
        }
    }
}
