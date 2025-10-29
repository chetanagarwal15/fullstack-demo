pipeline {
    agent any

    tools {
        nodejs 'NodeJS'  // Optional, if you have NodeJS tool configured
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/chetanagarwal15/fullstack-demo.git',
                    credentialsId: 'github-token'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                cd backend && npm install
                cd ../frontend && npm install
                '''
            }
        }

        stage('Run Backend Tests') {
            steps {
                sh '''
                cd backend
                npm test -- --ci --reporters=default --reporters=jest-junit --outputFile=test-results/junit.xml
                '''
            }
        }

        stage('Run Frontend Tests') {
            steps {
                sh '''
                cd frontend
                npm test -- --ci --reporters=default --reporters=jest-junit --outputFile=test-results/junit.xml
                '''
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                cd frontend
                npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQubeServer') {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                        sh """
                        cd backend
                        ${tool('SonarScanner')}/bin/sonar-scanner \
                            -Dsonar.projectKey=backend \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'backend/test-results/*.xml'
            junit allowEmptyResults: true, testResults: 'frontend/test-results/*.xml'
        }
    }
}
