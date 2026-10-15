pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'praveen272006/college-event-management'
        DOCKER_TAG = 'latest'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'

                bat 'docker build -t %DOCKER_IMAGE%:%DOCKER_TAG% .'
            }
        }

        stage('Verify Docker Image') {
            steps {
                echo 'Verifying Docker image'

                bat 'docker images %DOCKER_IMAGE%'
            }
        }

        stage('Login and Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '''
                        @echo off
                        echo Logging in to Docker Hub...
                        echo %DOCKER_PASS% | docker login -u "%DOCKER_USER%" --password-stdin

                        if errorlevel 1 exit /b 1

                        echo Pushing Docker image...
                        docker push %DOCKER_IMAGE%:%DOCKER_TAG%

                        if errorlevel 1 exit /b 1

                        echo Docker image pushed successfully.
                        docker logout
                    '''
                }
            }
        }

        stage('Verify Deployment Image') {
            steps {
                echo 'Checking the Kubernetes deployment configuration'

                bat 'kubectl get deployment college-event-deployment'
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD pipeline failed. Check Console Output.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}