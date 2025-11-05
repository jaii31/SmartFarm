pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/jaii31/SmartFarm.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh 'docker build -t smartfarm/data-ingestor:latest ./services/data-ingestor'
                    sh 'docker build -t smartfarm/analytics-processor:latest ./services/analytics-processor'
                    sh 'docker build -t smartfarm/dashboard:latest ./services/dashboard'
                }
            }
        }

        stage('Run Containers') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.yml up -d'
                }
            }
        }
    }

    post {
        success {
            echo '✅ SmartFarm system deployed successfully!'
        }
        failure {
            echo '❌ Build failed — check Docker or Jenkins logs.'
        }
    }
}
