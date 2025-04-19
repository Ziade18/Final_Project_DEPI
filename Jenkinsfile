pipeline {
    agent any

    environment {
        DOCKER_HUB = credentials('docker-hub-credentials')
        APP_NAME = "jpetstore"
        DOCKERHUB_USER = "ziad10"
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ziade18/Final_Project_DEPI.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package -P tomcat90'
            }
        }

        stage('Test') {
            steps {
                sh './mvnw test -P tomcat90'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_USER}/${APP_NAME}:${TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image("${DOCKERHUB_USER}/${APP_NAME}:${TAG}").push()
                    }
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                script {
                    sh "ansible-playbook ansible/deploy.yml -i 'localhost,' -e 'image_name=${DOCKERHUB_USER}/${APP_NAME}:${TAG}'"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}