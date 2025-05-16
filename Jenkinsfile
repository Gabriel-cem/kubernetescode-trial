pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'gabcem/test'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = 'dockerhub'
        GIT_CREDENTIALS = 'jenkins-ssh'
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: "${env.GIT_CREDENTIALS}", url: 'https://github.com/Gabriel-cem/kubernetescode-trial.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', "${env.REGISTRY_CREDENTIALS}") {
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    sh """
                    sed -i 's|image: ${DOCKER_IMAGE}:.*|image: ${DOCKER_IMAGE}:${DOCKER_TAG}|' k8s/deployment.yaml
                    git config user.name "jenkins"
                    git config user.email "jenkins@ci.local"
                    git add k8s/deployment.yaml
                    git commit -m "Update image tag to ${DOCKER_TAG}"
                    git push origin main
                    """
                }
            }
        }
    }

    post {
        failure {
            echo 'La pipeline ha fallado.'
        }
        success {
            echo 'Pipeline completada con éxito.'
        }
    }
}
