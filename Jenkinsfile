pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "dikshi13/devops-cicd-project"
        DOCKER_IMAGE_TAG = "v1"
    }

    stages {
        stage('Clone Code from GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/dikshi13/devops-cicd-project.git'
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
                sh "docker build -t $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG ."
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    sh "docker push $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG"
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
            echo " CI/CD Pipeline Successfully Executed!"
        }
        failure {
            echo "Pipeline Failed. Check the logs!"
        }
    }
}

