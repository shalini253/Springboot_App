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
        stage('Check Docker') {
            steps {
                script {
                    // Check if Docker is available on the Jenkins agent
                    try {
                        sh 'docker --version'
                        echo 'Docker is available.'
                    } catch (Exception e) {
                        echo 'Docker is not available, stopping build.'
                        error('Stopping the build as Docker is not available.')
                    }
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    try {
                        dockerImage = docker.build("shalini253/springboot-app:1.0")
                        echo 'Docker image built successfully.'
                    } catch (Exception e) {
                        echo "Failed to build Docker image: ${e.message}"
                        error('Failed to build Docker image.')
                    }
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    try {
                        docker.withRegistry('https://index.docker.io/v1/', 'DOCKERHUB_CREDENTIALS') {
                            dockerImage.push()
                        }
                        echo 'Docker image pushed successfully.'
                    } catch (Exception e) {
                        echo "Failed to push Docker image: ${e.message}"
                        error('Failed to push Docker image.')
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                try {
                    withKubeConfig([credentialsId: 'KUBE_CONFIG']) {
                        sh 'kubectl apply -f kubernetes/deployment.yaml'
                        sh 'kubectl apply -f kubernetes/service.yaml'
                        echo 'Application deployed to Kubernetes successfully.'
                    }
                } catch (Exception e) {
                    echo "Failed to deploy to Kubernetes: ${e.message}"
                    error('Failed to deploy to Kubernetes.')
                }
            }
        }
    }
    post {
        always {
            echo 'This will always run regardless of the result of the pipeline.'
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}

