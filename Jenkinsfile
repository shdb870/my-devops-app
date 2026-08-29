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
                sh 'docker build -t my-devops-app:ci-${BUILD_NUMBER} .'
            }
        }
		                stage('Docker run') {
            steps {
		sh 'docker rm -f my-devops-test || true'
                sh 'docker run -d --name my-devops-test --network jenkins-net -p 5001:5000 my-devops-app:ci-${BUILD_NUMBER}'
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
