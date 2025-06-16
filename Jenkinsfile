pipeline {
    agent any

    environment {
        APP_REPO = 'https://github.com/FazeelaBatool/notes_3.git'
        TEST_REPO = 'https://github.com/FazeelaBatool/notes_test.git'
        APP_IMAGE = 'notes-app'
        TEST_IMAGE = 'notes-app-tests'
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
                    echo "🧹 Cleaning up any old containers..."
                    docker rm -f notes-running || true

                    echo "🚀 Starting Notes App container..."
                    docker run -d --name notes-running -p 8081:8080 ${APP_IMAGE}

                    echo "⏳ Waiting for the app to start..."
                    sleep 30
                '''
            }
        }

        stage('✅ Run Selenium Tests') {
            steps {
                sh '''
                    echo "🧪 Running tests..."
                    docker run --rm --network host ${TEST_IMAGE}
                '''
            }
        }
    }

    post {
        always {
            echo "🧹 Cleaning up app container..."
            sh 'docker rm -f notes-running || true'
        }
    }
}
