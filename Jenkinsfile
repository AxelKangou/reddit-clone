 pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "reddit-clone-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "axelkangou4791"
        DOCKER_PASS = "xxx"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"

    }

    stages {
        stage ('clean workspace') {
            steps {
                cleanWs()
            }
             
        }
        stage ('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/AxelKangou/reddit-clone'
            }
        }
        stage ('SonarQube Analysis'){
            steps {
                withSonarQubeEnv('SonarQube-Server'){
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=reddit-clone \
                    -Dsonar.projectKey=reddit-clone'''                    
                }
            }
        }
        stage ('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-token'
                }
                
            }
        }
        stage ('Install dependencies'){
            steps {
                sh "npm install"
            }
        }
        stage ('Trivy FS scan') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }

    }


 }
