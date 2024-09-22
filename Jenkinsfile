pipeline {
    agent any 
    tools {
        maven 'M2_HOME'
    }
    environment {
       BRANCH_NAME = 'main'
       SCANNER_HOME = tool 'sonar-tool'
       GIT_URL = 'https://github.com/kserge2001/geoapp.git'
       GITHUB_CREDENTIALS = 'github-Credentials'
       SONAQUBE_CRED = 'Sonar-cred'
       SONAQUBE_INSTALLATION = 'Sonar-server'
       APP_NAME = 'geoapp'
  
       }

       stages{
        stage('Checkout') {
            steps {
              git branch: "${BRANCH_NAME}",\
               credentialsId: "${GITHUB_CREDENTIALS}", \
              url:  "${GIT_URL}"

            }
       }
       stage('unit test') {
            steps {
              sh 'mvn clean'
              sh 'mvn test'
              sh 'mvn compile'
            }
       }
       stage('Sonarqube Scan'){
            steps{
                withSonarQubeEnv(credentialsId: "${SONAQUBE_CRED}", \
                installationName: "${SONAQUBE_INSTALLATION}" ) {
              sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=${APP_NAME} -Dsonar.projectKey=${APP_NAME} \
                   -Dsonar.java.binaries=. '''
}
            }
        }
        stage('Quality Gate Check'){
            steps{
              script{
                 waitForQualityGate abortPipeline: false, credentialsId: "${SONAQUBE_CRED}" 
              }
            }
        }



}
}
