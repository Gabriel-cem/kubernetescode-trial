pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'gabcem/test'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('dockerhub')
        GIT_CREDENTIALS = credentials('jenkins-ssh')
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: "${env.GIT_CREDENTIALS}", url: 'https://github.com/Gabriel-cem/kubernetescode-trial.git'
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
