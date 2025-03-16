pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://13.127.106.128:9000'  // Your SonarQube URL
        SONARQUBE_TOKEN = credentials('sonar-token') // Jenkins Credentials ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/dikshi13/devops-cicd-project.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Code Quality Check') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar -Dsonar.host.url=${SONARQUBE_URL} -Dsonar.login=${SONARQUBE_TOKEN}'
                }
            }
        }

        stage('Run Selenium Test Cases') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t dikshi13/devops-app:v1 .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-creds', url: '']) {
                    sh 'docker push dikshi13/devops-app:v1'
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                sh 'ansible-playbook -i inventory deploy.yml'
            }
        }
    }

    post {
        success {
            echo ' Pipeline Successfully Completed '
        }

        failure {
            echo 'Pipeline Failed! Check Logs'
        }
    }
}

