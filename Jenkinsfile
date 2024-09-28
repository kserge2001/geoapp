pipeline {
    agent any 
    tools {
        maven 'M2_HOME'
    }
    environment {
       AWS_REGION = 'us-east-1'
       BRANCH_NAME = 'main'
       SCANNER_HOME = tool 'sonar-tool'
       GIT_URL = 'https://github.com/kserge2001/geoapp.git'
       GITHUB_CREDENTIALS = 'github-Credentials'
       SONAQUBE_CRED = 'Sonar-cred'
       SONAQUBE_INSTALLATION = 'Sonar-server'
       APP_NAME = 'geoapp'
       JFROG_CRED = 'jfrog-cred'
       ARTIFACTPATH = 'target/*.jar'
       ARTIFACTORY_URL = 'http://54.89.255.66:8081/artifactory'
       REPO = 'geolocation'
       ARTIFACTTARGETPATH = 'release_${BUILD_ID}.jar'
       DOCKER_REPO = '076892551558.dkr.ecr.us-east-1.amazonaws.com/devops'
       REPO_URL = '076892551558.dkr.ecr.us-east-1.amazonaws.com'
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

       /*
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
        stage('Trivy Scan'){
            steps{
                 sh "trivy fs --format table -o maven_dependency.html ."
            }
        }
        */
        stage('package app'){
            steps{
              sh 'mvn package'
              
            }
        }
            stage('Upload Jar to Jfrog'){
            steps{
                withCredentials([usernamePassword(credentialsId: "${JFROG_CRED}", \
                 usernameVariable: 'ARTIFACTORY_USER', passwordVariable: 'ARTIFACTORY_PASSWORD')]) {
                    script {
                        // Define the artifact path and target location
                        //def artifactPath = 'target/*.jar'
                        //def targetPath = "release_${BUILD_ID}.jar"

                        // Upload the artifact using curl
                        sh """
                            curl -u ${ARTIFACTORY_USER}:${ARTIFACTORY_PASSWORD} \
                                 -T ${ARTIFACTPATH} \
                                 ${ARTIFACTORY_URL}/${REPO}/${ARTIFACTTARGETPATH}
                        """
            }
        }
    }

}

    stage('Docker image Build'){
        steps{
            script{
           sh "docker build --no-cache -t ${DOCKER_REPO}:latest ."
           sh "docker build --no-cache -t ${DOCKER_REPO}:${BUILD_ID} ."
            }
           
        }
    }
  
    stage('Scan Docker Image'){
        steps{
          sh """trivy image --format table -o docker_image_report.html ${DOCKER_REPO}:${BUILD_ID}"""
  
        }
    }
  
    stage('Push Image To Registry'){
        steps{
          script{
        //def ecr_passwrd=sh(script: "aws ecr-public get-login-password --region 'us-east-1'")
         //sh "docker login --username AWS --password ${ecr_passwrd} public.ecr.aws/g0j7o9l5"   
        sh "/usr/local/bin/aws ecr-public get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${REPO_URL}"
        sh "docker push ${DOCKER_REPO}:latest "
        sh "docker push ${DOCKER_REPO}:${BUILD_ID} "
        }
    }
    }

      
}
}
