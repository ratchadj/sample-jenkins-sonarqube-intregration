pipeline {
  agent {
    node {
      label 'test-sonarqube dev'
    }
  }
  stages {
    stage('SCM') {
      git 'https://github.com/ratchadj/test-sonarqube.git'
    }
    stage('SonarQube analysis') {
      echo '${WORKSPACE}'
      def scannerHome = tool 'sonarqube-scanner';
      withSonarQubeEnv('sonarqube-server') {
        sh "${scannerHome}/bin/sonar-scanner " +
          "-Dsonar.projectKey=magento2:demo:pipeline " +
          "-Dsonar.projectName=Magento2-Demo-Project2-pipeline " +
          "-Dsonar.sources=. " +
          "-Dsonar.projectVersion=1.0 " +
          "-Dsonar.language=php " +
          "-Dsonar.sources=./code " +
          "-Dsonar.sourceEncoding=UTF-8 "
      }
    }
    stage("Quality Gate") {
      timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
        def qualitygate = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
        if (qualitygate.status != 'OK') {
            error "Pipeline aborted due to quality gate failure: ${qualitygate.status}"
        }
      }
    }
    stage('Build') {
      steps {
        echo 'Step Build'
      }
    }
    stage('Test') {
      steps {
        echo 'Step Test'
      }
    }
  }
  post { 
    failure { 
      script {
        slackSend color: 'danger', message: "$JOB_NAME - Failed #$BUILD_NUMBER (<$BUILD_URL|Open>)"
      }
    }
  }
}