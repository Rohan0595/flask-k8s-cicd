pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "rohanbandari/flask-cicd-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        def appImage = docker.build("${DOCKER_IMAGE}:${IMAGE_TAG}")
                        appImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig-file']) {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                    sh 'kubectl rollout restart deployment/flask-cicd-app'
                }
            }
        }
    }
}
