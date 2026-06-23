pipeline {
    agent any

    environment {
        IMAGE_NAME = "jyothikakotakadi/flask-cicd"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$BUILD_NUMBER
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f flask-app || true

                docker run -d \
                --name flask-app \
                -p 5000:5000 \
                $IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }
    }
}
