pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jaii31/SmartFarm.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Building Docker image for data-ingestor..."
                docker build -t smartfarm/data-ingestor:latest SmartFarm/services/data-ingestor
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                echo "Removing any existing container..."
                docker rm -f data-ingestor || true

                echo "Starting new container with docker-compose..."
                docker-compose -f SmartFarm/docker-compose.yml up -d --build
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build and deployment successful!'
        }
        failure {
            echo '❌ Build failed — check Docker or Jenkins logs.'
        }
    }
}
