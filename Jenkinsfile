pipeline {
    agent any

    stages {

        stage('Start') {
            steps {
                echo 'Hello from Jenkins'
            }
        }

        stage('Test') {
            steps {
                echo 'Running application tests...'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t shdb870/my-devops-app:ci-${BUILD_NUMBER} .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push shdb870/my-devops-app:ci-${BUILD_NUMBER}'
            }
        }

        stage('Docker Run') {
            steps {
                sh 'docker rm -f my-devops-test || true'
                sh 'docker run -d --name my-devops-test --network jenkins-net -p 5002:5000 shdb870/my-devops-app:ci-${BUILD_NUMBER}'
            }
        }

        stage('Health Check') {
            steps {
                sh 'curl -f http://my-devops-test:5000/health'
            }
        }
    }

    post {
        always {
            sh 'docker rm -f my-devops-test || true'
        }
    }
}
