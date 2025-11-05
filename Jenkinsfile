pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/jaii31/SmartFarm.git'
            }
        }

        stage('Build Docker Image') {
    steps {
        script {
            sh 'docker build -t smartfarm/data-ingestor:latest ./SmartFarm/services/data-ingestor'
        }
    }
}


        stage('Run Containers') {
            steps {
                script {
                    sh 'docker-compose -f SmartFarm/docker-compose.yml up -d'
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
