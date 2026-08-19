@Library('shared-lib') _

pipeline {
    agent any
    environment {
        IMAGE_NAME = 'yourdockerhub/reactjs-app'
        TAG        = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Clone') {
            steps { cloneRepo('https://github.com/eymengsim/devops_lab.git') }
        }
        stage('Sonar Scan') {
            steps { scanSonarqube('reactjs-app') }
        }
        stage('Build') {
            steps { buildDocker(type: 'reactjs', imageName: env.IMAGE_NAME, tag: env.TAG) }
        }
        stage('Push') {
            steps { pushDocker(env.IMAGE_NAME, env.TAG) }
        }
        stage('Deploy') {
            steps {
                deployDocker(imageName: env.IMAGE_NAME, tag: env.TAG,
                              containerName: 'reactjs-app', port: '3000:80')
            }
        }
    }
    post {
        success { sendTelegram("✅ ${env.IMAGE_NAME}:${env.TAG} deployed") }
        failure { sendTelegram("❌ Build failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}") }
    }
}