pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE = 'benlevi/flask-todo-app'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clean workspace before cloning
                cleanWs()
                // Clone the repository
                git branch: 'main',
                    url: 'https://github.com/Ben-levi/Web-App-To-Do---Jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                    // Login to DockerHub
                    bat 'echo %DOCKERHUB_CREDENTIALS_PSW% | docker login -u %DOCKERHUB_CREDENTIALS_USR% --password-stdin'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // Push the image to DockerHub
                    bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                // Deploy using docker-compose
                bat 'docker-compose up -d'
            }
        }
    }

    post {
        always {
            // Logout from DockerHub
            bat 'docker logout'
            
            // Clean up images to save space
            script {
                bat "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
}
