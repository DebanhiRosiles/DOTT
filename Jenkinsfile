  
pipeline {
  agent any
  stages {
    stage('First') {
      steps {
        sh '  cd /home/cloud_user/DOTT/python'
        sh ' echo "First Stage" '
        sh ' sudo docker build -t pym . '
        sh ' sudo docker run -ti -p 8000:8000 pym '
    }

    stage('Second') {
      steps {
        sh ' echo "Updating Second Stage" '
      }
    }

    stage('Third') {
      environment {
        SCANNER_HOME = tool 'FP-sonarCloud-scanner'
        ORGANIZATION = "DebanhiRosiles"
        PROJECT_NAME = "DOTT"
      } 
    steps {
        withSonarQubeEnv('FP-sonarCloud-server') {
            sh ' echo "Third Stage" '
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
            -Dsonar.java.binaries=build/classes/java/ \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.sources=.'''
        }
    }

  }
}
