pipeline {
    agent any

    environment {
        SONARQUBE_TOKEN = credentials('sonar-token')
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/dikshi13/devops-cicd-project.git'
            }
        }

        stage('Maven Build & Test Cases') {
            steps {
                sh 'mvn clean package'
                sh 'mvn test'
            }
        }

        stage('SonarQube Code Analysis') {
            when {
                branch 'main'
            }
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t dikshith13/devops-cicd-project:${BUILD_NUMBER} ."
            }
        }

        stage('Docker Hub Login') {
            when {
                branch 'main'
            }
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    echo "DockerHub Login Successful!"
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            when {
                branch 'main'
            }
            steps {
                sh "docker push dikshith13/devops-cicd-project:${BUILD_NUMBER}"
            }
        }

        stage('Ansible Deployment to EC2') {
            when {
                branch 'main'
            }
            steps {
                sh 'ansible-playbook -i inventory deploy.yml'
            }
        }
    }

    post {
        always {
            junit 'target/surefire-reports/*.xml'
        }

        success {
            echo " Build Successful!"
        }

        failure {
            echo "Build Failed!"
        }
    }
}

