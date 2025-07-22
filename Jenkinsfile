pipeline {
    agent any
    tools {
        maven 'maven_3_5_0'
    }
    environment {
        TELEGRAM_BOT_TOKEN = '8070085401:AAFTtUxHLOw-O9nwxLfjMKPi4_ZJN2BokGg'
        TELEGRAM_CHAT_ID = '802486159' 
    }
    stages {
        stage('Build Maven') {
            steps {
                script {
                    try {
                        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Zongheang/devops-automation']]])
                        sh 'mvn clean install'
                        notifyTelegram("✅ Maven build completed successfully.")
                    } catch (e) {
                        notifyTelegram("❌ Maven build failed: ${e.message}")
                        throw e
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        sh 'docker build -t songhieng2k25/devops-integration .'
                        notifyTelegram("✅ Docker image built successfully.")
                    } catch (e) {
                        notifyTelegram("❌ Docker build failed: ${e.message}")
                        throw e
                    }
                }
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                script {
                    try {
                        withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                            sh 'docker login -u songhieng2k25 -p ${dockerhubpwd}'
                        }
                        sh 'docker push songhieng2k25/devops-integration'
                        notifyTelegram("✅ Docker image pushed to DockerHub.")
                    } catch (e) {
                        notifyTelegram("❌ Docker push failed: ${e.message}")
                        throw e
                    }
                }
            }
        }
    }
}

def notifyTelegram(String message) {
    sh """
    curl -s -X POST https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage \\
        -d chat_id=${TELEGRAM_CHAT_ID} \\
        -d text="${message}"
    """
}