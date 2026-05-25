pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/tanay25/python-security-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate

                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '''
                . venv/bin/activate
                pytest
                '''
            }
        }

        stage('Bandit Scan') {
            steps {
                sh '''
                . venv/bin/activate

                bandit -r . \
                -x venv,__pycache__,tests \
                || true
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
               script {
                   def scannerHome = tool 'sonar-scanner'

                   withSonarQubeEnv('sonarqube') {
                   sh """
                   . venv/bin/activate

                   ${scannerHome}/bin/sonar-scanner
                   """
                }
            }
        }
    }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t python-security-project:latest .'
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image python-security-project:latest'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker stop python-container || true
                docker rm python-container || true

                docker run -d \
                --name python-container \
                -p 5000:5000 \
                python-security-project:latest
                '''
            }
        }
    }
}