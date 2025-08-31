pipeline {
    agent any

    environment {
        BASE_PATH = "/var/www/analytics.trustamaster.ae"
        REPO_URL = "https://github.com/Codeyiizen/analytics"
        PM2_APP_NAME = "analytics"
        PORT = "3001"
    }

    stages {
        stage('Clone Repository') {
            steps {
                dir("${BASE_PATH}") {
                    // If repo already exists, fetch latest changes; otherwise clone
                    sh '''
                    if [ -d ".git" ]; then
                        git reset --hard
                        git clean -fd
                        git pull origin main
                    else
                        git clone ${REPO_URL} .
                    fi
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                dir("${BASE_PATH}") {
                    sh "npm install --legacy-peer-deps"
                }
            }
        }

        stage('Build Project') {
            steps {
                dir("${BASE_PATH}") {
                    sh "npm run build"
                }
            }
        }

        stage('Start Application') {
            steps {
                dir("${BASE_PATH}") {
                    // Stop existing pm2 process if it exists and start new one
                    sh """
                    pm2 delete ${PM2_APP_NAME} || true
                    pm2 start npm --name ${PM2_APP_NAME} -- run start -- --port ${PORT}
                    pm2 save
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
