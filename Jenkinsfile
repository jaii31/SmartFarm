cd "F:\B.Tech 3rd Year\1st Sem\CCD\Mini Project\SmartFarm"

@"
pipeline {
  agent any
  environment {
    IMAGE_TAG = "smartfarm-data-ingestor:latest"
    COMPOSE_FILE = "docker-compose.yml"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build data-ingestor image') {
      steps {
        sh '''
          docker build -t ${IMAGE_TAG} ./services/data-ingestor || true
        '''
      }
    }
    stage('Start stack') {
      steps {
        sh '''
          if [ -f "${COMPOSE_FILE}" ]; then
            docker-compose -f ${COMPOSE_FILE} up -d --build || true
          else
            echo "docker-compose.yml not found; skipping compose"
          fi
        '''
      }
    }
  }
  post {
    always {
      sh 'docker ps --filter name=data-ingestor || true'
    }
  }
}
"@ > Jenkinsfile
