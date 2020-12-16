pipeline {
  agent any
  stages {
    stage('Third') {
      environment {
        SCANNER_HOME = tool 'FP-sonarCloud-scanner'
        ORGANIZATION = "debanhirosiles"
        PROJECT_NAME = "DebanhiRosiles_DOTT"
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
    
     stage('First') {
      steps {
        sh ' cd /home/cloud_user/DOTT/python/ '
        sh ' echo "First Stage" '
        sh ' sudo docker build -d pym . '
        sh ' sudo docker run -p 8000:8000 pym '
      }
       
    stage('Second') {
      steps {
        sh ' echo "Second Stage" '
        sh ' cd /home/cloud_user/DOTT/python/ '
        sh ' python api.py'
      }
    }
    
   
    }
    
  }
}
