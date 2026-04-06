pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t tourism-website:${BUILD_NUMBER} .'
      }
    }

    stage('Run Container Smoke Test') {
      steps {
        sh '''
          docker rm -f tourism-ci-smoke >/dev/null 2>&1 || true
          docker run -d --name tourism-ci-smoke -p 18080:80 tourism-website:${BUILD_NUMBER}
          sleep 3
          curl -fsS http://127.0.0.1:18080/health
        '''
      }
    }
  }

  post {
    always {
      sh 'docker rm -f tourism-ci-smoke >/dev/null 2>&1 || true'
    }
    success {
      echo 'Jenkins pipeline completed successfully.'
    }
    failure {
      echo 'Pipeline failed. Check stage logs for details.'
    }
  }
}
