pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'benl89/todo_app'
        DOCKER_TAG = 'latest'
        // Define DockerHub credentials properly
        DOCKERHUB = credentials('dockerhub-credentials')
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clean workspace before cloning
                cleanWs()
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                node(env.NODE_NAME) {
                    bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                node(env.NODE_NAME) {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                        bat "docker login -u %DOCKERHUB_USERNAME% -p %DOCKERHUB_PASSWORD%"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                node(env.NODE_NAME) {
                    bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                node(env.NODE_NAME) {
                    bat 'docker-compose up -d'
                }
            }
        }
    }

    post {
        always {
            node(env.NODE_NAME) {
                bat 'docker logout'
            }
        }
    }
}
