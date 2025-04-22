pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'shivaays/html-ci:latest'
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
  }

  stages {
    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/yourusername/yourrepo.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE .'
      }
    }

    stage('Login to Docker Hub') {
      steps {
        sh '''
          echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        '''
      }
    }

    stage('Push Docker Image') {
      steps {
        sh 'docker push $DOCKER_IMAGE'
      }
    }

    stage('Deploy via WSL2') {
      steps {
        // Replace "shubh" with your actual WSL2 username if different
        bat '''
          wsl.exe -d Ubuntu -- bash -c "
            docker pull $DOCKER_IMAGE &&
            cd /mnt/d/dockerProject &&
            docker-compose down &&
            docker-compose up -d
          "
        '''
      }
    }
  }

  post {
    failure {
      echo '❌ Deployment failed.'
    }
    success {
      echo '✅ Deployment successful. App is live on http://localhost:8081'
    }
  }
}
