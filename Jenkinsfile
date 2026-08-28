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
    }
}
