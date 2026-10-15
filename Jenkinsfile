pipeline {
    agent any

    environment {
        IMAGE_NAME = 'praveen272006/college-event-management'
        DEPLOYMENT_NAME = 'college-event-deployment'
        CONTAINER_NAME = 'college-event-container'
        K8S_SERVICE = 'college-event-service'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out project source code'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                    docker build -t %IMAGE_NAME%:latest .
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Test Docker Image') {
            steps {
                bat '''
                    docker image inspect %IMAGE_NAME%:latest
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-push',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '''
                        @echo off
                        echo %DOCKER_PASS% | docker login -u "%DOCKER_USER%" --password-stdin
                        if errorlevel 1 exit /b 1

                        docker push %IMAGE_NAME%:latest
                        if errorlevel 1 exit /b 1

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                    kubectl set image deployment/%DEPLOYMENT_NAME% %CONTAINER_NAME%=%IMAGE_NAME%:latest
                    if errorlevel 1 exit /b 1

                    kubectl rollout status deployment/%DEPLOYMENT_NAME% --timeout=120s
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '''
                    kubectl get deployments
                    kubectl get pods
                    kubectl get services
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Website image built, pushed, and deployment updated.'
        }

        failure {
            echo 'FAILURE: Pipeline failed. Checking Kubernetes rollout status.'

            bat '''
                kubectl rollout status deployment/college-event-deployment --timeout=30s
            '''

            script {
                echo 'If deployment fails, inspect the Jenkins console and Kubernetes events.'
            }
        }

        always {
            echo 'CI/CD pipeline execution completed.'
        }
    }
}