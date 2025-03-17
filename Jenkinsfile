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

        stage('Maven Build & Test') {
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
                    sh 'mvn sonar:sonar -Dsonar.login=${SONARQUBE_TOKEN}'
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
                sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
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
            junit '**/target/surefire-reports/*.xml'
        }

        success {
            echo " Build Successful!"
        }

        failure {
            echo " Build Failed!"
        }
    }
}

