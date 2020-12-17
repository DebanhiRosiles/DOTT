pipeline {
  agent any
  stages {
   stage('First') {
      steps {
        sh ' cd /home/cloud_user/DOTT/python/ '
        sh ' echo "First Stage: check that the IMAGE Dockerfile its runing" '
        script{   
            try{
              sh ' sudo docker image inspect pym:latest'
            }//end try check image
            catch(exc){
               try{
                sh ' sudo lsof -8000'
               }//end try to check port be in use
               catch(exc){
                sh ' echo "Making build of IMAGE to run" ' 
                sh ' sudo docker build -t pym . '
                sh ' sudo  docker run  -d -p 8000:8000 pym '
              }//end catch to build and run image
            }//end catch to check port avaliable
        }//end script
      }//end step 
    }//end first stage
  
   stage('Second') {
       environment {
        SCANNER_HOME = tool 'FP-sonarCloud-scanner'
        ORGANIZATION = "debanhirosiles"
        PROJECT_NAME = "DebanhiRosiles_DOTT"
      }//end environment var 
      steps {
        sh ' echo "Second Stage: make a coverage xml for the tests.py and send to sonarCloud" '
        sh ' cd /home/cloud_user/DOTT/python/ '
        sh ' sudo apt install python3-pip'
        sh ' sudo python3 -m pip install coverage '
        sh ' coverage run /home/cloud_user/DOTT/python/tests.py | coverage report | coverage xml '//do coverage xml  
        withSonarQubeEnv('FP-sonarCloud-server') {
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
            -Dsonar.java.binaries=build/classes/java/ \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.python.coverage.reportPaths=*coverage.xml '''
        }//end SonarQube proccess
      }//end steps
    }// End stage Second
  
    stage('Third') {
      environment {
        SCANNER_HOME = tool 'FP-sonarCloud-scanner'
        ORGANIZATION = "debanhirosiles"
        PROJECT_NAME = "DebanhiRosiles_DOTT"
      } //end environment var 
      steps {
        withSonarQubeEnv('FP-sonarCloud-server') {
            sh ' echo "Third Stage> make a test on SonarCloud" '
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
            -Dsonar.java.binaries=build/classes/java/ \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.sources=.'''
        }//end SonarQube proccess
      }//end steps
    }//end stage Third
  }//end stages
}//end pipeline
