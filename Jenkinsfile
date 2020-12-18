pipeline {
  agent any
  environment {
     SCANNER_HOME = tool 'FP-sonarCloud-scanner'
   } //end environment var 
  stages {
   stage('Build') {
      steps {
        sh ' cd $WORKPLACE'
        sh ' echo "First Stage: try to build IMAGE Dockerfile " '
        script{   
            try{
              sh ' ls'
              sh '  docker build -f ./python/Dockerfile -t pym . '
              env.NEXTSTAGE='1'
            }//end try check image
            catch(exc){
               sh ' echo "Couldnt build the image" '
               env.NEXTSTAGE='0'
            }//end catch to check port avaliable
        }//end script
      }//end step 
    }//end first stage
    
    stage('SonarCloudTest') {
      when{
        environment name: 'NEXTSTAGE', value: '1'
      }
      steps {
        script{
          withCredentials([
            string(
              credentialsId: 'SC_Proyect',
              variable: 'PROJECT_NAME'  ),
            string(
              credentialsId: 'SC_Org',
              variable: 'ORGANIZATION'  ),
          ])
          {
            sh ' cd $WORKSPACE/ '
            sh ' pwd '
            sh ' echo "SonarCloudTest Stage: make a coverage xml for the tests.py (unit-test) and send to sonarCloudv " '
            sh ' sudo apt install python3-pip'
            sh ' sudo python3 -m pip install coverage '
            sh ' sudo python3 -m pip install pytest '
            sh ' coverage run -m pytest ./python/tests.py -v | coverage report | coverage xml'
            withSonarQubeEnv('FP-sonarCloud-server') {
              sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
              -Dsonar.java.binaries=build/classes/java/ \
              -Dsonar.projectKey=$PROJECT_NAME \
              -Dsonar.python.coverage.reportPaths=$WORKSPACE/coverage.xml'''
            }//end SonarQube proccess||*cov*.xml
            env.QG=waitForQualityGate().status
          }//end {} in script
          // -Dsonar.sources=. \
          //-Dsonar.language=py \
        }//end script
      }//end steps
    }//end stage Third
    
    stage('Deployment') {
      when{ 
        environment name: 'QG', value: 'OK'
      }//end when
      steps{
        sh 'echo "Deployment stage starts" '
        script{
          try{
            sh ' docker rm -f $(sudo docker ps | grep ash | awk \'{print $1}\') '
          }catch(docRun){
            sh 'echo "PYM docker image is not running" '
          }
           try{
              sh ' docker run -d -p 8000:8000 pym'
            }catch(portDen){
               sh 'echo "The port 8000 is not avalaiable" '
               sh ' docker run -d pym'
            }
        }//end script
      }//end steps
    }//end stage Deployment
  }//end stages
}//end pipeline
