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
                    string(
                        credentialsId: 'dockerhub-pat',
                        variable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat 'docker login -u dpdevipriyaa1037 -p %DOCKER_PASSWORD%'

                    bat 'docker push %DOCKER_IMAGE%:%BUILD_NUMBER%'

                    bat 'docker push %DOCKER_IMAGE%:latest'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying application to AWS EC2...'

                sshagent(credentials: ['ec2-ssh']) {
                    bat '''
                        ssh -o StrictHostKeyChecking=no ubuntu@15.252.70.219 "docker pull dpdevipriyaa1037/devops-capstone-app:latest && docker stop devops-capstone-app || true && docker rm devops-capstone-app || true && docker run -d --name devops-capstone-app -p 3000:3000 dpdevipriyaa1037/devops-capstone-app:latest"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD build and deployment completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}