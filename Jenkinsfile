pipeline {
    agent any

    /* Ensures Jenkins provides Maven instead of relying on system */
    tools {
        maven 'Maven-3'          // Must match Global Tool Configuration name
    }

    environment {
        IMAGE_NAME = "YOUR_DOCKERHUB_USERNAME/spring-app"
        TAG = "latest"
    }

    stages {

        stage('Build JAR') {
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

    /* Prevents pipeline from failing silently & improves cleanup */
    post {
        always {
            cleanWs()
        }
        failure {
            echo 'Pipeline failed ❌ Check console output.'
        }
        success {
            echo 'Pipeline completed successfully ✅'
        }
    }
}
