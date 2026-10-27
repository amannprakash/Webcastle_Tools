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

        stage('Verify Project Files') {
            steps {
                sh '''
                    test -f "$WORKSPACE/$APP_DIR/package.json"
                    test -f "$WORKSPACE/$APP_DIR/package-lock.json"
                    echo "Project files verified."
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    docker run --rm \
                      -v jenkins_home:/jenkins_home \
                      -w "/jenkins_home/workspace/${JOB_NAME}/${APP_DIR}" \
                      "$NODE_IMAGE" \
                      npm ci --no-audit --no-fund
                '''
            }
        }

        stage('Build Next.js App') {
            steps {
                sh '''
                    docker run --rm \
                      -v jenkins_home:/jenkins_home \
                      -w "/jenkins_home/workspace/${JOB_NAME}/${APP_DIR}" \
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
            echo 'FAILED: Check Console Output for details.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
