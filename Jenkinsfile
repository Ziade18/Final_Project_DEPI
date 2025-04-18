pipeline {
    agent any

    environment {
        IMAGE_NAME = 'ziade18/jpetstore'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Ziade18/Final_Project_DEPI.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Run Tests') {
            steps {
                sh './mvnw test'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-creds', url: '') {
                    script {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy using Ansible') {
            steps {
                sh 'ansible-playbook -i inventory.ini playbook.yml'
            }
        }
    }
}
