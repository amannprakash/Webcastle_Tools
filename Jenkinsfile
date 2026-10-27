pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        APP_DIR = 'webcastle-tools-main'
        NODE_IMAGE = 'node:22-alpine'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Docker') {
            steps {
                sh 'docker version'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    docker run --rm \
                      -v "$WORKSPACE/$APP_DIR:/app" \
                      -w /app \
                      "$NODE_IMAGE" \
                      npm ci
                '''
            }
        }

        stage('Build Next.js App') {
            steps {
                sh '''
                    docker run --rm \
                      -v "$WORKSPACE/$APP_DIR:/app" \
                      -w /app \
                      "$NODE_IMAGE" \
                      npm run build
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Next.js production build completed.'
        }
        failure {
            echo 'FAILED: Check the console output for the failing stage.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
