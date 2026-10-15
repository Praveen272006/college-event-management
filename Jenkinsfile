pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out College Event Management project'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image'
                sh 'docker build -t college-event-management:latest .'
            }
        }

        stage('Pipeline Complete') {
            steps {
                echo 'Build stage completed successfully'
            }
        }
    }
}