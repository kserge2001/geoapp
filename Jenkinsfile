pipeline {
    agent any 
    tools {
        maven 'M2_HOME'
    }
    environment {
       BRANCH_NAME = 'main'
       GIT_URL = 'https://github.com/kserge2001/geoapp.git'
       GITHUB_CREDENTIALS = 'github-Credentials'

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
}

}
