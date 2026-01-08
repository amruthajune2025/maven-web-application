pipeline
{
    agent any

    tools
    {
        maven 'Maven_3.9.9'
    }

    environment
    {
        buildNumber = "${BUILD_NUMBER}"
    }

    stages
    {
        stage('Checkout Code to Jenkins from GitHub')
        {
            steps()
            {
                git branch: 'DevopsJulyBatch', url: 'https://github.com/amruthajune2025/maven-web-application.git'
            }
        }

        stage('Build Artifact using Maven')
        {
            steps()
            {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image')
        {
            steps()
            {
                sh 'docker build -t 040983495414.dkr.ecr.ap-southeast-2.amazonaws.com/login/application:${buildNumber} .'
            }
        }
#deployment
        stage('Authenticate and Push Docker Image to AWS ECR')
        {
            steps()
            {
                sh 'aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin 040983495414.dkr.ecr.ap-southeast-2.amazonaws.com/login/application'
                sh 'docker push 040983495414.dkr.ecr.ap-southeast-2.amazonaws.com/login/application:${buildNumber}'
            }
        }

        stage('Remove Docker Image from Jenkins Locally')
        {
            steps()
            {
                sh 'docker rmi -f 040983495414.dkr.ecr.ap-southeast-2.amazonaws.com/login/application:${buildNumber}'
            }
        }

        stage('Update Image Tag in Kubernetes Manifest')
        {
            steps()
            {
                sh "sed -i 's/Build_Tag/${buildNumber}/g' MavenWebApplication.yaml"
            }
        }

        stage('Deploy Application in AWS EKS Cluster')
        {
            steps()
            {
                sh 'kubectl delete deployment webpage-deployment -n production || true'
                sh 'kubectl apply -f MavenWebApplication.yaml'
            }
        }
    }
}





