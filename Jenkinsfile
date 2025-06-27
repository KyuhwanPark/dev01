pipeline {
    agent any

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
                // Docker CLI 가 없으면 에러 내기
                sh '''
                  if ! command -v docker >/dev/null; then
                    echo "ERROR: docker CLI not found"
                    exit 1
                  fi
                  docker build -t ${DOCKER_IMAGE} .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDS_ID}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                      docker push ${DOCKER_IMAGE}
                    '''
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
