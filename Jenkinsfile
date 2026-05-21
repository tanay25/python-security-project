pipeline{
    agent any

    stages{
        stage('Clone Code'){
            steps{
                git branch: 'main', url:'https://github.com/tanay25/python-security-project.git'

                }
            }
        stage('Install Dependencies'){
            steps{
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Run Unit Tests'){
            steps{
                sh 'pytest'
            }
        }
        stage('Bandit Scan'){
            steps{
                sh 'bandit -r .'
            }
        }
        stage('SonarQube Analysis'){
            steps{
                withSonarQubeEnv('sonarqube') {
                    sh 'sonar-scanner'
                }
            }
        }
        stage("Build Docker Image"){
            steps{
                sh 'docker build -t python-security-project .'
            }
        }
        stage('Trivy Image Scan'){
            steps{
                sh 'trivy image python-security-project:latest'
            }
        }
        stage('Run Container'){
            steps{
                sh '''
                docker stop python-container || true
                docker rm python-container || true
                docker run -d --name python-container -p 5000:5000 python-security-project:latest
                '''
            }
        }
            
    }
}