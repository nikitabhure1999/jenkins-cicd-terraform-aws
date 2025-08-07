pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        APP_DIR = 'react-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/nikitabhure1999/jenkins-cicd-terraform-aws.git'
            }
        }

        stage('Install Node Modules') {
            steps {
                dir("${APP_DIR}") {
                    sh 'npm install'
                }
            }
        }

        stage('Build React App') {
            steps {
                dir("${APP_DIR}") {
                    sh 'npm run build'
                }
            }
        }

        stage('Terraform Init') {
            steps {
                dir('modules') {
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir('modules') {
                    sh 'terraform plan'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('modules') {
                    sh 'terraform apply -auto-approve'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Completed Successfully!'
        }
        failure {
            echo '❌ Build Failed. Check logs!'
        }
    }
}

