pipeline {

    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out Docker project from GitHub...'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t jenkins-docker-demo:${BUILD_NUMBER} .
                    docker tag jenkins-docker-demo:${BUILD_NUMBER} jenkins-docker-demo:latest
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                    docker rm -f jenkins-docker-container || true
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker run -d \
                        --name jenkins-docker-container \
                        -p 8081:80 \
                        jenkins-docker-demo:latest
                '''
            }
        }

        stage('Verify Website') {
            steps {
                sh '''
                    sleep 3
                    curl -f http://127.0.0.1:8081/
                '''
            }
        }

        stage('Docker Status') {
            steps {
                sh '''
                    docker ps
                '''
            }
        }
    }

    post {

        success {
            echo 'Docker image built and container deployed successfully!'
            echo 'Website is available on port 8081.'
        }

        failure {
            echo 'Docker pipeline failed!'
        }
    }
}
