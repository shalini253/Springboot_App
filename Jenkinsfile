pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('Docker-Pat')
        KUBE_CONFIG = credentials('Config_Pat')
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/shalini253/Springboot_App.git'
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    echo 'Starting Docker build...'
                    dockerImage = docker.build("shalini253/springboot-app:1.0")
                    echo 'Docker build completed successfully.'
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    echo 'Starting Docker push...'
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKERHUB_CREDENTIALS') {
                        dockerImage.push()
                    }
                    echo 'Docker push completed successfully.'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                withKubeConfig([credentialsId: 'KUBE_CONFIG']) {
                    sh 'kubectl apply -f kubernetes/deployment.yaml'
                    sh 'kubectl apply -f kubernetes/service.yaml'
                }
                echo 'Deployment to Kubernetes completed successfully.'
            }
        }
    }
}


