pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mydockerimage'
        DOCKER_REPO = 'dikshi13/devops-cicd-project'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'doc

