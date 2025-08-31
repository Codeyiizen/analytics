pipeline {
  agent any
  stages {
    stage('Clone Repository') {
      steps {
        dir(path: "${BASE_PATH}") {
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
        dir(path: "${BASE_PATH}") {
          sh 'npm install --legacy-peer-deps'
        }

      }
    }

    stage('Build Project') {
      steps {
        dir(path: "${BASE_PATH}") {
          sh 'npm run build'
        }

      }
    }

    stage('Start Application') {
      steps {
        dir(path: "${BASE_PATH}") {
          sh """
                                        pm2 delete ${PM2_APP_NAME} || true
                                        pm2 start npm --name ${PM2_APP_NAME} -- run start -- --port ${PORT}
                                        pm2 save
                                        """
        }

      }
    }

  }
  environment {
    BASE_PATH = '/var/www/analytics.trustamaster.ae'
    REPO_URL = 'https://github.com/Codeyiizen/analytics'
    PM2_APP_NAME = 'analytics'
    PORT = '3001'
  }
  post {
    success {
      echo 'Deployment completed successfully!'
    }

    failure {
      echo 'Deployment failed!'
    }

  }
}