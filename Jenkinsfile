pipeline {
    agent any

    environment {
        IMAGE_NAME = "lohar45/akshay-ansibl"  // MUST be lowercase
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

        stage('Debug Files') {
    steps {
        sh 'ls -la'
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
        sh '''
        ANSIBLE_HOST_KEY_CHECKING=False \
        ansible-playbook -i inventory.ini deploy.yml
        '''
    }
}

    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully ✅'
        }
        failure {
            echo 'Pipeline failed ❌ Check console output'
        }
    }
}
