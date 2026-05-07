pipeline {
    agent any

    environment {
        CONTAINER_NAME = 'ml-pipeline-api'
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker compose down || true'
            }
        }

        stage('Run Pipeline (Train + Serve)') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    sleep 10
                    curl -f http://localhost:8000/metrics || exit 1
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded! API is live at port 8000.'
        }
        failure {
            echo 'Pipeline failed. Check logs.'
            sh 'docker compose logs'
        }
    }
}
