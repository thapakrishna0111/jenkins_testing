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
