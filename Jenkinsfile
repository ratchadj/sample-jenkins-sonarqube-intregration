pipeline {
  agent {
    docker {
      image 'node:7-alpine'
    }

  }
  stages {
    stage('Test') {
      steps {
        sh 'node --version'
        echo 'nom version'
      }
    }
    stage('Proceed') {
      steps {
        input(message: '"Proceed"', ok: 'Proceed', id: 'proceed')
      }
    }
  }
}