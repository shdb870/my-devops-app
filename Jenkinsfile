pipeline {
    agent any

    stages {

        stage('Start') {
            steps {
                echo 'Hello from Jenkins'
            }
        }

        stage('Build Info') {
            steps {
                echo "Job: ${JOB_NAME}"
                echo "Build Number: ${BUILD_NUMBER}"
                echo "Workspace: ${WORKSPACE}"
                echo "Git Commit: ${GIT_COMMIT}"
            }
        }

        stage('Test') {
            steps {
                echo 'Running application tests...'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t shadab870/my-devops-app:ci-${BUILD_NUMBER} .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push shadab870/my-devops-app:ci-${BUILD_NUMBER}'
            }
        }

        stage('Docker Run') {
            steps {
                sh 'docker rm -f my-devops-test || true'
                sh 'docker run -d --name my-devops-test --network jenkins-net -p 5002:5000 shadab870/my-devops-app:ci-${BUILD_NUMBER}'
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    for i in {1..10}; do
                        if curl -f http://my-devops-test:5000/health; then
                            echo "Health check passed"
                            exit 0
                        fi

                        echo "Application not ready, retrying..."
                        sleep 2
                    done

                    echo "Health check failed"
                    exit 1
                '''
            }
        }
    }

    post {
        always {
            sh 'docker rm -f my-devops-test || true'
        }
    }
}
