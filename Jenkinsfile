pipeline {
    agent { label 'jenkins-agent' }
    
    tools {
        jdk 'java17'
        maven 'Maven3'
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/HEMALATHA-V-DEV/registration-app'
            }
        }
        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('Test Application') {
            steps {
                sh "mvn test"
            }
        }
        stage('Sonarqube analysis') {  // Added missing stage
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                    sh "mvn sonar:sonar"

                }
                

                }

                // Add deployment steps here
            }
        }
        stage('Quality Gate Check') { 
            steps {
                script {
                    waitForQualityGate(abortPipeline: false, credentialsId: 'sonarqube-jenkins-token')
                }
            }
        }

    }
}
