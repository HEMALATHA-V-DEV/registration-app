pipeline {
    agent { label 'jenkins-agent' }
    
    tools {
        jdk 'java17'
        maven 'Maven3'
    }

    environment {
        // Docker-related environment variables
        APP_NAME = 'register-app-pipeline'              // Application name
        RELEASE = '1.0.0'                               // Release version
        DOCKER_USER = 'hemalathav20'                     // Docker username
        DOCKER_PASS = 'dockerhub'                        // Docker password (add actual password or credentials ID)
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"        // Docker image name
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
        
        stage('SonarQube analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage('Quality Gate Check') { 
            steps {
                script {
                    waitForQualityGate(abortPipeline: false, credentialsId: 'sonarqube-jenkins-token')
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }

        // New stages added
        stage("Trivy Scan") {
            steps {
                script {
                    sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ashfaque9x/register-app-pipeline:latest --no-progress --scanners vuln --exit-code 0 --severity HIGH,CRITICAL --format table')
                }
            }
        }

        stage('Cleanup Artifacts') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }

        stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user clouduser:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-13-232-128-192.ap-south-1.compute.amazonaws.com:8080/job/gitops-register-app-cd/buildWithParameters?token=gitops-token'"
                }
            }
        }
    }

    post {
        failure {
            emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
                     subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
                     mimeType: 'text/html', to: "ashfaque.s510@gmail.com"
        }
        success {
            emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
                     subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
                     mimeType: 'text/html', to: "ashfaque.s510@gmail.com"
        }      
    }
}
