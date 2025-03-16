pipeline {
    agent any
    
    environment {
        GIT_CREDENTIALS = 'github-token'
        SONARQUBE_SERVER = 'SonarQube'    // SonarQube server name from Jenkins configuration
        DOCKER_IMAGE = 'myapp:latest'
    }

    stages {

        stage('Clone Code from GitHub') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/dikshi13/devops-cicd-project.git',
                    credentialsId: "${GIT_CREDENTIALS}"
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
                sh 'docker build -t myapp:latest .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-credentials', url: '']) {
                    sh 'docker tag myapp:latest yourdockerhubusername/myapp:latest'
                    sh 'docker push yourdockerhubusername/myapp:latest'
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                sh 'ansible-playbook -i ansible/inventory ansible/deploy.yml'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}

