pipeline {
    agent any

    tools {
        maven 'Maven_3.9.7'
    }

    environment {
        buildNumber = "${BUILD_NUMBER}"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'DevOpsJulyBatch', url: 'https://github.com/MithunTechnologiesDevOps/maven-web-application.git'
            }
        }

        stage('Build Project') {
            steps {
                sh 'mvn clean package'
            }
        }
// deleting image locally to utilize resources hi
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t amrutha2016/dockerpipeline:${buildNumber} .'
            }
        }

        stage('Push Docker Image to DockerHub Registry') {
            steps {
                withCredentials([string(credentialsId: 'Docker_Hub_Password', variable: 'Docker_Hub_Password')]) {
                    sh """
                    echo \$Docker_Hub_Password | docker login -u amrutha2016 --password-stdin
                    docker push amrutha2016/dockerpipeline:${buildNumber}
                    """
                }
            }
        }

        stage('Delete Docker Image Locally in Jenkins Build Server') {
            steps {
                sh 'docker rmi -f amrutha2016/dockerpipeline:${buildNumber}'
            }
        }
stage('Deploy Application to Docker Deployment Server')
        {
            steps()
            {
                sshagent(['DeploymentServer_SSH']) 
                {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.107.53.94 docker rm -f mavenwebapplication || true"
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.107.53.94 docker run -d --name mavenwebapplication -p 8080:8080 amrutha2016/dockerpipeline:${buildNumber}"
                }
            }
         }
    }
}