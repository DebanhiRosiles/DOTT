pipeline {
  agent any
  environment {
     SCANNER_HOME = tool 'FP-sonarCloud-scanner'
     script{
       withCredentials([
         string(
           credentialsId: 'SC_Proyect',
           variable: 'PROJECT_NAME'  ),
         string(
           credentialsId: 'SC_Org',
           variable: 'ORGANIZATION'
         ),
     }//end script
   } //end environment var 
  stages {
   stage('First') {
      steps {
        sh ' cd python/ '
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
      steps {
        script{
          {
            withSonarQubeEnv('FP-sonarCloud-server') {
            sh ' echo "Second Stage> make a test on SonarCloud" '
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
            -Dsonar.java.binaries=build/classes/java/ \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.sources=.'''
           }//end SonarQube proccess
           }
        }//script end
      }//end steps
    }// End stage Second
  
    stage('Third') {
      steps {
            script{
              {
                sh ' cd $WORKSPACE '
                sh ' echo "Third Stage: make a coverage xml for the tests.py and send to sonarCloud" '
                sh ' sudo apt install python3-pip'
                sh ' sudo python3 -m pip install coverage '
                sh ' sudo python3 -m pip install pytest '
                sh ' coverage run -m pytest python/tests.py -v | coverage report | coverage xml'
                withSonarQubeEnv('FP-sonarCloud-server') {
                  sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
                  -Dsonar.java.binaries=build/classes/java/ \
                  -Dsonar.projectKey=$PROJECT_NAME \
                  -Dsonar.python.coverage.reportPaths=$WORKSPACE/coverage.xml'''
                }//end SonarQube proccess||*cov*.xml
                
              }//end {} in script
                 // -Dsonar.sources=. \
                  //-Dsonar.language=py \
            }//end script
        }//end steps
    }//end stage Third
    stage('Deployment') {
      steps{
            sh ' echo "Deployment Stage" '
      }//end steps
    }//end stage Deployment
  }//end stages
}//end pipeline
