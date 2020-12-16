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
    
     stage('Second') {
       environment {
        SCANNER_HOME = tool 'FP-sonarCloud-scanner'
        ORGANIZATION = "debanhirosiles"
        PROJECT_NAME = "DebanhiRosiles_DOTT"
      } 
      steps {
        sh ' echo "Second Stage" '
        sh ' cd /home/cloud_user/DOTT/python/ '
        sh ' sudo apt install python3-pip'
        sh ' sudo python3 -m pip install coverage '
        sh ' coverage run /home/cloud_user/DOTT/python/tests.py | coverage report | coverage xml'
        withSonarQubeEnv('FP-sonarCloud-server') {
            sh ' echo "Third Stage" '
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
            -Dsonar.java.binaries=build/classes/java/ \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.sources= /home/cloud_user/DOTT/python
            sonar.python.coverage.reportPath=tests.py'''
        }
        sh ' python api.py'
      }
    }
    
     stage('First') {
      steps {
        sh ' cd /home/cloud_user/DOTT/python/ '
        sh ' echo "First Stage" '
        sh ' sudo docker build -t pym . '
        sh ' sudo  docker run  -d -p 8000:8000 pym '
        
      }
    }
  }
}
