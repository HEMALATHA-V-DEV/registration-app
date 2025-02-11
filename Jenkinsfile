pipeline {
    agent { label 'jenkins-agent' }
    
    tools {
        jdk 'java17'
        maven 'Maven3'
    }

    environment {
        // Docker-related environment variables
        APP_NAME = 'register-app-pipeline'                // Application name
        RELEASE = '1.0.0'                                 // Release version
        DOCKER_USER = 'hemalathav20'                       // Docker username
        DOCKER_PASS = 'dockerhub'                                   // Docker password (add actual password or credentials ID)
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"         // Docker image name
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"         // Docker tag combining release version and build number
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

        stage('build & push the docker image'){
            steps{
                script {
                    docker.withRegistry('', 'DOCKER_PASS') {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }


                    docker.withRegistry('', 'DOCKER_PASS') {
                        docker_image.push ("${IMAGE_TAG}")
                        docker_image.push ("latest")
                    }
                }
            }
        }
    }
}

          

