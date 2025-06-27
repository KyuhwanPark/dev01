pipeline {
    agent any

    environment {
        GIT_REPO   = 'https://github.com/KyuhwanPark/dev01.git'
        GIT_BRANCH = 'main'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO}", credentialsId: 'github-pat'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '/opt/homebrew/bin/docker build -t parkkyuhwan/dev01:1.0 .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push parkkyuhwan/dev01:1.0'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f dev01-deploy.yaml'
                sh 'kubectl apply -f dev01-svc.yaml'
            }
        }
    }
}
