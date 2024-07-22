pipeline {
    agent any  // Specifies that the pipeline can run on any available Jenkins agent

    environment {
        // Define environment variables
        DOCKER_IMAGE = 'shalini253/springboot-app'  // Match this to your Docker Hub repository and image name
        DOCKER_TAG = "${env.BUILD_NUMBER}"  // Use Jenkins build number as the tag for better version control
        REGISTRY = 'docker.io'  // Docker Hub registry
    }

    stages {
        stage('Test Credentials') {
            steps {
                script {
                    // Testing credentials binding
                    withCredentials([usernamePassword(credentialsId: 'DockerToken', usernameVariable: 'REGISTRY_USER', passwordVariable: 'REGISTRY_PASS')]) {
                        echo "Attempting to log in as: $REGISTRY_USER"
                        sh 'echo "*** Credentials bound successfully ***"'
                    }
                }
            }
        }

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

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t ${REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'DockerToken', usernameVariable: 'REGISTRY_USER', passwordVariable: 'REGISTRY_PASS')]) {
                        sh "echo $REGISTRY_PASS | docker login -u $REGISTRY_USER --password-stdin $REGISTRY"
                        sh "docker push ${REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'Config_Pat', variable: 'KUBECONFIG')]) {
                        // Set the kubeconfig for Kubernetes commands
                        sh 'kubectl version'
                        sh 'kubectl get nodes'
                        sh "kubectl apply -f kubernetes/service.yaml --kubeconfig=$KUBECONFIG"
                        sh "kubectl apply -f kubernetes/deployment.yaml --kubeconfig=$KUBECONFIG"
                    }
                }
            }
        }
    }

    post {
        always {
            // Actions to always perform after the pipeline runs
            script {
                sh 'docker logout'
                echo 'Clean up actions or other always-executed steps could be placed here.'
            }
        }

        success {
            // Actions to perform if the pipeline succeeds
            echo 'Deployment was successful.'
        }

        failure {
            // Actions to perform if the pipeline fails
            echo 'An error occurred during the build or deployment process.'
        }
    }
}
