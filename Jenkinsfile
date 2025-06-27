pipeline {
    agent {
        docker {
            image 'docker:24.0.0-dind'
            // Docker 데몬에 접근하기 위해 소켓 마운트 및 privileged 모드로 실행
            args  '-v /var/run/docker.sock:/var/run/docker.sock --privileged'
        }
    }

    environment {
        GIT_REPO        = 'https://github.com/KyuhwanPark/dev01.git'
        GIT_BRANCH      = 'main'
        DOCKER_IMAGE    = 'parkkyuhwan/dev01:1.0'
        DOCKER_CREDS_ID = 'dockerhub-creds'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDS_ID}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
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

    post {
        always {
            cleanWs()
        }
    }
}
