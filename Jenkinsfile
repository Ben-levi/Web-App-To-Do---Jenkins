pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'benlevi/flask-todo-app'
        DOCKER_TAG = 'latest'
    }
    
    stages {
        stage('Debug Information') {
            steps {
                bat 'dir'  // List files to verify workspace contents
                bat 'type Dockerfile'  // Display Dockerfile contents
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build image with explicit path
                    bat """
                        cd %WORKSPACE%
                        docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    bat 'docker login -u %DOCKERHUB_USERNAME% -p %DOCKERHUB_PASSWORD%'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                bat """
                    cd %WORKSPACE%
                    docker-compose up -d
                """
            }
        }
    }

    post {
        always {
            bat 'docker logout'
        }
    }
}
