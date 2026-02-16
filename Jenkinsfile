pipeline {
    agent any
    
    tools {
        maven 'maven'    
    }
    environment {
        IMAGE_NAME = "YOUR_DOCKERHUB_USERNAME/spring-app"
        TAG = "latest"
    }

    stages {

        stage('Build JAR (Dockerized Maven)') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${TAG} .'
            }
        }

        stage('Docker Hub Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh 'echo ${PASS} | docker login -u ${USER} --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${TAG}'
            }
        }

        stage('Remove Local Image') {
            steps {
                sh 'docker rmi ${IMAGE_NAME}:${TAG} || true'
            }
        }

        stage('Deploy via Ansible') {
            steps {
                sh 'ansible-playbook deploy.yml'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
