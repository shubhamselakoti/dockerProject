pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'shivaays/html-ci'
  }

  stages {
    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/shubhamselakoti/dockerProject.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        echo '🛠️ Building Docker image...'
        bat "docker build -t %DOCKER_IMAGE% ."
      }
    }

    stage('Deploy via WSL2') {
      steps {
        echo '🚀 Running docker-compose inside WSL2...'
        bat '''
          wsl.exe -d Ubuntu -- bash -c "
            cd /mnt/d/dockerProject &&
            docker-compose down &&
            docker-compose up -d
          "
        '''
      }
    }

    stage('Show Webpage Link') {
      steps {
        writeFile file: 'web-link.html', text: '''
          <html>
            <body>
              <h2>✅ Web App is Live!</h2>
              <p><a href="http://localhost:8081" target="_blank">👉 Open Webpage</a></p>
            </body>
          </html>
        '''
        publishHTML(target: [
          reportName: 'Open Web App',
          reportDir: '.',
          reportFiles: 'web-link.html',
          keepAll: true,
          alwaysLinkToLastBuild: true,
          allowMissing: false
        ])
      }
    }
  }

  post {
    success {
      echo '✅ Your web app is live at http://localhost:8081'
    }
  }
}
