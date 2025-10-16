pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = 'ugiramahirwe/node-web-app' // Your Docker Hub repo
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Build App') {
            steps {
                powershell 'npm install'
            }
        }

        stage('Test App') {
            steps {
                powershell 'npm test'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                powershell """
                docker build -t $env:IMAGE_NAME:$env:IMAGE_TAG .
                echo $env:DOCKERHUB_CREDENTIALS_PSW | docker login -u $env:DOCKERHUB_CREDENTIALS_USR --password-stdin
                docker push $env:IMAGE_NAME:$env:IMAGE_TAG
                """
            }
        }

        stage('Deploy App') {
            steps {
                powershell """
                # Stop existing container if running
                docker stop node-web-app -ErrorAction SilentlyContinue

                # Remove existing container if exists
                docker rm node-web-app -ErrorAction SilentlyContinue

                # Pull latest image
                docker pull $env:IMAGE_NAME:$env:IMAGE_TAG

                # Run the container
                docker run -d -p 3000:3000 --name node-web-app $env:IMAGE_NAME:$env:IMAGE_TAG
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
