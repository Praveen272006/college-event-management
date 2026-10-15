pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out College Event Management'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'
                bat 'docker build -t college-event-management:latest .'
            }
        }

        stage('Verify Docker Image') {
            steps {
                bat 'docker images college-event-management'
            }
        }
    }
}