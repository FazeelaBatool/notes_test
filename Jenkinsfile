pipeline {
    agent any

    environment {
        APP_REPO = 'https://github.com/FazeelaBatool/notes_3.git'
        TEST_REPO = 'https://github.com/FazeelaBatool/notes_test.git'
        APP_IMAGE = 'notes-app'
        TEST_IMAGE = 'notes-app-tests'
        RECEIVER_EMAIL = 'qasimalik@gmail.com'
    }

    stages {
        stage('📦 Checkout Notes App') {
            steps {
                dir('app') {
                    git branch: 'main', url: "${APP_REPO}"
                }
            }
        }

        stage('📦 Checkout Test Code') {
            steps {
                dir('tests') {
                    git branch: 'main', url: "${TEST_REPO}"
                }
            }
        }

        stage('🔨 Build App Docker Image') {
            steps {
                dir('app') {
                    sh 'docker build -t ${APP_IMAGE} .'
                }
            }
        }

        stage('🔨 Build Test Docker Image') {
            steps {
                dir('tests') {
                    sh 'docker build -t ${TEST_IMAGE} .'
                }
            }
        }

        stage('🚀 Run Notes App Container') {
            steps {
                sh '''
                    echo "🧹 Cleaning up old containers..."
                    docker rm -f notes-running || true

                    echo "🚀 Starting Notes App..."
                    docker run -d --name notes-running -p 8081:8080 ${APP_IMAGE}

                    echo "⏳ Waiting for app to start..."
                    sleep 40
                '''
            }
        }

        stage('✅ Run Selenium Tests') {
            steps {
                sh '''
                    echo "🧪 Running test container..."
                    docker run --rm --network host ${TEST_IMAGE}
                '''
            }
        }
    }

    post {
        success {
            emailext(
                subject: "✅ Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "All Selenium tests for the Notes App passed successfully.\n\n🔗 ${env.BUILD_URL}",
                to: "${RECEIVER_EMAIL}"
            )
        }
        failure {
            emailext(
                subject: "❌ Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Some test cases failed. Please check Jenkins logs.\n\n🔗 ${env.BUILD_URL}",
                to: "${RECEIVER_EMAIL}",
                attachLog: true
            )
        }
    }
}
