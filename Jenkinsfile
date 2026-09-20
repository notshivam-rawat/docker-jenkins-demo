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
                    echo "Waiting for Nginx container..."

                    for i in 1 2 3 4 5 6 7 8 9 10
                    do
                        echo "Attempt $i..."

                        if curl --noproxy '*' -f http://127.0.0.1:8081/; then
                            echo ""
                            echo "Website is running successfully!"
                            exit 0
                        fi

                        echo "Website not ready yet. Waiting..."
                        sleep 2
                    done

                    echo "Website failed to respond after multiple attempts."
                    exit 1
                '''
            }
        }

        stage('Docker Status') {
            steps {
                sh '''
                    echo "Running containers:"
                    docker ps

                    echo "Docker images:"
                    docker images
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
