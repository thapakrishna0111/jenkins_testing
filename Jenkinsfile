pipeline {
    agent any
    environment {
    PATH="$PATH:/opt/maven/bin"
    }
    stages {
      stage('git checkout'){
        steps{
           git branch: 'main', url: 'https://github.com/thapakrishna0111/jenkins_testing.git'
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
      stage('sonar quality check'){

      agent{
        docker {
          image 'maven'
          args '-u root'
        }
      }
      steps{
        script{
          withSonarQubeEnv(credentialsId: 'sonar-token') {
            sh 'mvn clean package sonar:sonar'
          }
        }

      }
    }

      stage('Test') {
        agent {
           docker { image 'node:18.16.0-alpine' }
	}

           steps {
                sh 'node --version'
            }
      }
    }
}
