pipeline {
    agent { label 'jenkins-agent' }
    tools {
      jdk 'java17'
      maven 'Maven3'
    stages {
        stage('cleanup workspace') {
            steps {
              cleanWs()
              
            }
        }
        stage('checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/HEMALATHA-V-DEV/registration-app'
            }
        }
        stage('build') {
            steps {
                sh "mvn clean package"  
            }
        }
        stage('test_application') {
            steps {
                sh "mvn test"
            }
        }
        stage()
    }
    }
}
