pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'dikshi13/devops-cicd-project'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Clone Code from GitHub') {
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
                    sh 'mvn sonar:sonar'
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
                sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                sh 'ansible-playbook -i inventory deploy.yml'
            }
        }
    }
}

