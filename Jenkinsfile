pipeline {
  agent any
  stages {
   stage('First') {
      steps {
        sh ' cd /home/cloud_user/DOTT/python/ '
        sh ' echo "First Stage: check that the IMAGE Dockerfile its runing" '
        script{   
            try{
              sh ' sudo docker build -t pym . '
            }//end try check image
            catch(exc){
               sh ' echo "Couldnt build the image" '
            }//end catch to check port avaliable
        }//end script
      }//end step 
    }//end first stage
  
   stage('Second') {
      environment {
        SCANNER_HOME = tool 'FP-sonarCloud-scanner'
      } //end environment var 
      steps {
        script{
          withCredentials([
            string(
              credentialsId: 'SC_Proyect',
              variable: 'PROJECT_NAME'
            ),
            string(
              credentialsId: 'SC_Org',
              variable: 'ORGANIZATION'
            ),
          ])
        }
        sh ' echo "Second Stage: make a coverage xml for the tests.py and send to sonarCloud" '
        sh ' cd /home/cloud_user/DOTT/python/ '
        
        try{
          sh ' sudo pip3 --version '
        }
        catch(exc){
          sh ' sudo apt install python3-pip'
        }
        sh ' sudo python3 -m pip install coverage '
        sh ' coverage run -m pytest /home/cloud_user/DOTT/python/tests.py -v | coverage report | coverage xml '//do coverage xml  
        withSonarQubeEnv('FP-sonarCloud-server') {
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
            -Dsonar.java.binaries=build/classes/java/ \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.python.coverage.reportPaths=**/coverage.xml '''
        }//end SonarQube proccess
      }//end steps
    }// End stage Second
  
    stage('Third') {
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
