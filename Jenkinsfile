pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                deleteDir()
                git branch: 'master', url: 'https://github.com/AjayKumar-91/brain-tasks.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t trendstore-app .'
            }
        }

        stage('Login & Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "DockerHub_Credentials",
                    passwordVariable: "dockerHubPass",
                    usernameVariable: "dockerHubUser"
                )]) {
                    sh '''
                        echo "$dockerHubPass" | docker login -u "$dockerHubUser" --password-stdin
                        docker tag trendstore-app $dockerHubUser/brain-tasks-app:latest
                        docker push $dockerHubUser/brain-tasks-app:latest
                    '''
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                    docker compose down || true
                    docker compose up -d --build
                '''
            }
        }

        stage('Verify') {
            steps {
                sh 'docker ps'
            }
        }
    }
}