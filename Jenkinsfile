pipeline {
  agent any
  stages {
    stage('Build') {

      steps {
        echo 'Step Build'
      }
    }
    stage('SonarQube analysis') {
      steps {
        echo '${WORKSPACE}'
        // requires SonarQube Scanner 2.8+
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
    }
    // No need to occupy a node
    stage("Quality Gate") {
      steps {
        timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
          def qualitygate = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
          if (qualitygate.status != 'OK') {
              error "Pipeline aborted due to quality gate failure: ${qualitygate.status}"
          }
        }
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