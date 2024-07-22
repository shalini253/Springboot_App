pipeline {
    agent any
    environment {
        DOCKERHUB_CREDS = credentials('DockerToken') // Ensure this matches the successful test pipeline
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
                    sh 'docker --version'
                    echo 'Docker is available.'
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build("shalini253/springboot-app:${env.BUILD_NUMBER}")
                    echo 'Docker image built successfully.'
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKERHUB_CREDS') {
                        dockerImage.push("${env.BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                    echo 'Docker image pushed successfully.'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'KUBE_CONFIG']) {
                        sh 'kubectl apply -f kubernetes/deployment.yaml'
                        sh 'kubectl apply -f kubernetes/service.yaml'
                        echo 'Application deployed to Kubernetes successfully.'
                    }
                }
            }
        }
    }
    post {
        always {
            script {
                sh 'docker logout'
                echo 'Logged out of Docker Hub.'
            }
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
