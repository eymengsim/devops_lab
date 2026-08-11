pipeline {
    agent any

    tools {
        nodejs 'NodeJS-22'
    }

    environment {
        IMAGE_NAME = "mengsim/maya-chen-portfolio"
        IMAGE_TAG  = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/eymengsim/devops_lab.git'
            }
        }

        stage('Install') {
            steps {
                sh 'rm -rf node_modules package-lock.json'
                sh 'npm cache clean --force'
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -t ${IMAGE_NAME}:latest ."
            }
        }

    stage('Docker Push') {
        steps {
            withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:latest
                '''
            }
        }
    }
    stage('Deploy') {
                steps {
                    sshagent(['deploy-server-ssh']) {
                        sh '''
                            ssh -o StrictHostKeyChecking=no root@100.26.21.233 \
                            "docker pull ${IMAGE_NAME}:latest && \
                            docker stop maya-portfolio || true && \
                            docker rm maya-portfolio || true && \
                            docker run -d --name maya-portfolio -p 8080:80 ${IMAGE_NAME}:latest"
                        '''
                }
            }
        }
    }
}