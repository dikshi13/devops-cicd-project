pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://<SonarQube-Server-IP>:9000' // SonarQube Server URL
        SONARQUBE_TOKEN = credentials('sonar-token') // SonarQube token stored in Jenkins credentials
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials') // Docker Hub Credentials ID in Jenkins
        DOCKER_IMAGE = 'myapp:latest'
        DOCKER_REPO = 'your-dockerhub-username/myapp'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.host.url=${SONARQUBE_URL} \
                    -Dsonar.login=${SONARQUBE_TOKEN}
                    '''
                }
            }
        }

        stage('Run Selenium Tests') {
            steps {
                sh 'mvn test' // Run Selenium Test Cases
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh "docker tag ${DOCKER_IMAGE} ${DOCKER_REPO}:latest"
                    sh "docker push ${DOCKER_REPO}:latest"
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                sh '''
                ansible-playbook -i inventory deploy.yml
                '''
            }
        }
    }
}

