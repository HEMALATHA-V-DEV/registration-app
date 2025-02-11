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
        stage('Deploy') {  // Added missing stage
            steps {
                echo "Deploying application..."
                // Add deployment steps here
            }
        }
    }
}
