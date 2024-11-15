pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                bat 'docker-compose build'
            }
        }
        
        stage('Test') {
            steps {
                bat 'docker-compose run flask-app python -m pytest'
            }
        }
        
        stage('Deploy') {
            steps {
                bat 'docker-compose up -d'
            }
        }
    }
    
    post {
        always {
            bat 'docker-compose logs'
        }
    }
}
