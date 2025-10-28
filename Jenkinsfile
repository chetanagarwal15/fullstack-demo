pipeline {
  agent any

  tools {
    nodejs "NodeJS_18"
  }

  environment {
    REPO = "https://github.com/chetanagarwal15/fullstack-demo.git"
    SONAR_TOKEN = credentials('sonar-token')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'cd backend && npm install'
        sh 'cd frontend && npm install'
      }
    }

    stage('Run Backend Tests') {
      steps {
        sh 'cd backend && npm test || true'
      }
    }

    stage('Run Frontend Tests') {
      steps {
        sh 'cd frontend && npm test -- --watchAll=false || true'
      }
    }

    stage('Build Frontend') {
      steps {
        sh 'cd frontend && npm run build'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('MySonarQubeServer') {
          sh '''
            cd backend
            sonar-scanner \
              -Dsonar.projectKey=backend \
              -Dsonar.sources=. \
              -Dsonar.host.url=$SONAR_HOST_URL \
              -Dsonar.login=$SONAR_TOKEN
          '''
        }
      }
    }
  }

  post {
    always {
      junit 'backend/test-results/**/*.xml'
      archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
    }
  }
}

