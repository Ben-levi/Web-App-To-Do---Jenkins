pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'benlevi/flask-todo-app'
        DOCKER_TAG = 'latest'
    }
    
    stages {
        stage('Verify Files') {
            steps {
                // Debug step to verify files are present
                bat '''
                    echo "Workspace contents:"
                    dir
                    echo "Requirements.txt contents:"
                    type requirements.txt
                '''
            }
            
        }
stage('Debug') {
    steps {
        bat 'echo %CD%'
        bat 'dir'
        bat 'git status'
    }
}
        stage('Build Docker Image') {
            steps {
                script {
                    // Ensure we're in the right directory
                    bat """
                        echo "Building Docker image..."
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
                bat 'docker-compose up -d'
            }
        }
    }

    post {
        always {
            bat 'docker logout'
            // Clean up images
            bat "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG} || exit 0"
        }
    }
}
