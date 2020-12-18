pipeline {
  agent any
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
          ]){
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
      environment {
        SCANNER_HOME = tool 'FP-sonarCloud-scanner'
      } //end environment var 
      steps {
            sh ' echo "Third Stage: make a coverage xml for the tests.py and send to sonarCloud" '
            sh ' cd python/ '
            sh ' sudo apt install python3-pip'
            sh ' sudo python3 -m pip install coverage '
            sh ' sudo python3 -m pip install pytest '
            
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
              {
                sh ' coverage run -m pytest $WORSKPACE/tests.py -v | coverage report -m tests.py -i | coverage xml -i'
                sh 'cat coverage.xml'
                sh 'cd $WORKSPACE '
                sh ' pwd '
                sh ' echo $WORKSPACE '
                withSonarQubeEnv('FP-sonarCloud-server') {
                  sh '''pwd
                  ls
                  $SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
                  -Dsonar.java.binaries=build/classes/java/ \
                  -Dsonar.projectKey=$PROJECT_NAME \
                  -Dsonar.sources=. \
                  -Dsonar.language=py \
                  -Dsonar.python.coverage.reportPaths=$WORKSPACE/coverage.xml'''
                }//end SonarQube proccess||*cov*.xml
              }//end {} in script
            }//end script
        }//end steps
    }//end stage Third
  }//end stages
}//end pipeline
