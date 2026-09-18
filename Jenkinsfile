pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'dpdevipriyaa1037/devops-capstone-app'
        DOCKER_HOST = 'tcp://localhost:2375'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Getting code from GitHub...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                bat 'docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .'

                bat 'docker tag %DOCKER_IMAGE%:%BUILD_NUMBER% %DOCKER_IMAGE%:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dpdevipriyaa1037',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat 'echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin'

                    bat 'docker push %DOCKER_IMAGE%:%BUILD_NUMBER%'

                    bat 'docker push %DOCKER_IMAGE%:latest'
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD build completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}