pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = "ap-south-1"
    }
    stages {
        stage('Checkout SCM'){
            steps{
                script{
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/jay75chauhan/Teraform-EKS-Jenkins']])
                }
            }
        }
        stage('Initializing Terraform'){
            steps{
                script{
                    dir('Teraform-EKS-deployment'){
                        sh 'terraform init'
                    }
                }
            }
        }
        stage('Formatting Terraform Code'){
            steps{
                script{
                    dir('Teraform-EKS-deployment'){
                        sh 'terraform fmt'
                    }
                }
            }
        }
        stage('Validating Terraform'){
            steps{
                script{
                    dir('Teraform-EKS-deployment'){
                        sh 'terraform validate'
                    }
                }
            }
        }
        stage('Previewing the Infra using Terraform'){
            steps{
                script{
                    dir('Teraform-EKS-deployment'){
                        sh 'terraform plan'
                    }
                    input(message: "Are you sure to proceed?", ok: "Proceed")
                }
            }
        }
        stage('Creating/Destroying an EKS Cluster'){
            steps{
                script{
                    dir('EKS') {
                        sh 'terraform $action --auto-approve'
                    }
                }
            }
        }
        stage('Deploying Nginx Application') {
            steps{
                script{
                    dir('Teraform-EKS-deployment/ConfigurationFiles') {
                        sh 'aws eks update-kubeconfig --name my-eks-cluster'
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                    }
                }
            }
        }
    }
}
