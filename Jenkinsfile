pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Pre-check') {
      steps {
        script {
          // ensure docker CLI is present on the agent
          def rc = sh(script: 'command -v docker >/dev/null 2>&1 || echo 1', returnStdout: true).trim()
          if (rc == '1') {
            error "docker CLI not found on this Jenkins agent. Make docker available or run Jenkins on a host with docker."
          }
        }
      }
    }

    stage('Build images') {
      steps {
        script {
          sh 'docker build -t smartfarm/dashboard:ci ./services/dashboard'
          // build data-ingestor only if folder exists
          if (fileExists('./services/data-ingestor')) {
            sh 'docker build -t smartfarm/data-ingestor:ci ./services/data-ingestor'
          } else {
            echo 'services/data-ingestor not found — skipping data-ingestor build'
          }
        }
      }
    }

    stage('Run containers') {
      steps {
        script {
          if (fileExists('docker-compose.yml')) {
            sh 'docker-compose -f docker-compose.yml up -d --build'
          } else {
            // run dashboard and data-ingestor (use different host ports if needed)
            sh 'docker rm -f smartfarm-dashboard || true || true'
            sh 'docker rm -f smartfarm-data-ingestor || true || true'
            sh 'docker run -d --name smartfarm-dashboard -p 8082:5000 smartfarm/dashboard:ci'
            if (fileExists('./services/data-ingestor')) {
              sh 'docker run -d --name smartfarm-data-ingestor -p 8083:5000 smartfarm/data-ingestor:ci'
            }
          }
        }
      }
    }

    stage('Smoke tests') {
      steps {
        script {
          // a minimal HTTP check for dashboard
          sleep 2
          sh 'curl -fsS --max-time 5 http://localhost:8082/status || (echo "dashboard not reachable" && exit 1)'
          echo 'smoke test passed'
        }
      }
    }
  }

  post {
    always {
      echo "Listing docker containers (if available)"
      sh 'docker ps || true'
    }
    success {
      echo "Pipeline finished SUCCESS"
    }
    failure {
      echo "Pipeline FAILED — check console output"
    }
  }
}
