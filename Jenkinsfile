pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('Dockerhub')
        DEPLOY_CREDENTIALS = credentials('Deploy')
    }
    stages {
        stage('Access GitHub') {
           steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'Demo', url: 'git@github.com:Andisimo1/test.git']]])
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t andisimo1/firstdemo:latest .'
            }
        }
        stage('Docker Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'docker push andisimo1/firstdemo:latest'
            }
        }
        stage('SSH') {
            steps {
                script {
                    sh """
                    #!/bin/bash
                    ssh -T -o StrictHostKeyChecking=no -i key.pem ubuntu@18.156.1.51 << EOF
                    docker pull andisimo1/firstdemo:latest
                    docker run -t -d -p 2222:5000 andisimo1/firstdemo:latest
                    exit 0
                    << EOF
                    """
                    }
                }
            }
        }
    }
