pipeline{
  agent any
    stages{
      stage('git checkout'){
        steps{
           git branch: 'main', url: 'https://github.com/thapakrishna0111/counter-app.git'
        }
      }
      stage('UNIT Testing'){
        steps{
           sh 'mvn test'
        }
      }
     stage('Integration Testing'){
        steps{
           sh 'mvn verify -DskipUnitTests'
        }
      }
     stage('Maven Build'){
        steps{
           sh 'mvn clean install'
        }
      }
     stage('SonarQube Analysis'){
        steps{
           script {
             withSonarQubeEnv(credentialsId: 'sonar-auth') {
	     sh 'mvn clean package sonar:sonar'
	     }
           }
        }
      }
    stage('Quality Gate Status'){
        steps{
           script {
             waitForQualityGate abortPipeline: false, credentialsId: 'sonar-auth'
           }
        }
      }
    stage('Upload to Nexus'){
        steps{
           script {
           def readPomVersion = readMavenPom file: 'pom.xml'
           def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
             nexusArtifactUploader artifacts: 
               [
                 [
                   artifactId: 'springboot', 
                   classifier: '', 
                   file: 'target/Uber.jar', 
                   type: 'jar'
                 ]
                ], 
                    credentialsId: 'Nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '192.168.1.8:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"   
             }
        }
      }
    stage('Docker Image Build'){
      steps{
        script {
          sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
          sh 'docker image tag ${JOB_NAME}:v1.${BUILD_ID} kt0111/${JOB_NAME}:v1.${BUILD_ID}'
          sh 'docker image tag ${JOB_NAME}:v1.${BUILD_ID} kt0111/${JOB_NAME}:latest'
          }
        }
      }
    stage('Push image to DockerHub'){
      steps{
        script {
            sh 'docker login -u kt0111 -p ${DOCKERHUB_PASSWORD}'
            sh 'docker image push kt0111/${JOB_NAME}:v1.${BUILD_ID}'
            sh 'docker image push kt0111/${JOB_NAME}:latest'
         
        }
      }
    }

    }

}
