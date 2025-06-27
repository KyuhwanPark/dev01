pipeline {
    agent any
    environment {
        PATH = "/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KyuhwanPark/dev01.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker --version
                docker pull nginx:alpine || true
                docker build -t parkkyuhwan/dev01:1.0 .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push parkkyuhwan/dev01:1.0
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f dev01-deploy.yaml
                kubectl apply -f dev01-svc.yaml
                '''
            }
        }
    }
}
