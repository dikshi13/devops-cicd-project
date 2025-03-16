pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SONARQUBE_URL = 'MySonarQube'
    }

    stages {
        stage('Clone Code from GitHub') {
            steps {
                git 'git 'https://github.com/dikshi13/devops-cicd-project.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Code Quality Check') {
            steps {
                withSonarQubeEnv('MySonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Run Selenium Test Cases') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy with Ansible') {
            steps {
                ansiblePlaybook(
                    playbook: 'ansible/deploy.yaml',
                    inventory: 'ansible/inventory'
                )
            }
        }
    }
}

