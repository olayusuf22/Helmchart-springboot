pipeline {
    agent any
    tools {
        maven "Maven3"
    }
    environment {
        registry = "308134718221.dkr.ecr.us-east-2.amazonaws.com/jenkins"
    }
    stages {
        stage("Checkout") {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/olatech22/springboot-app-1']]])
            }
        }
        stage("Build Jar") {
            steps {
                sh "mvn clean install"
            }
        }
        stage ("Build Image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        stage ("Push into ECR") {
            steps {
                sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 308134718221.dkr.ecr.us-east-2.amazonaws.com"
                sh "docker push 308134718221.dkr.ecr.us-east-2.amazonaws.com/jenkins:latest"
            }
        }
        
        stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install mychart --namespace helm-deploy --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}