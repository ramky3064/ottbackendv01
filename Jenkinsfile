pipeline {
    agent any

    tools {
        nodejs 'node20'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/ramky3064/ottbackendv01.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build') {
            steps {
                bat 'npm run build'
            }
        }

        stage('Test') {
            steps {
                bat 'npm run test -- --coverage'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonar-scanner'
                    withSonarQubeEnv('local-sonar') {
                        withEnv(['SONAR_SCANNER_OPTS=-Xmx2048m -Xms512m']) {
                            bat "${scannerHome}\\bin\\sonar-scanner.bat"
                        }
                    }
                }
            }
        }

        stage('Verify PM2') {
            steps {
                bat 'where pm2'
                bat 'pm2 -v'
            }
        }

        stage('Deploy with PM2') {
            steps {
                bat '''
                echo Deploying with PM2...
                pm2 delete ott-backend || exit /B 0
                pm2 start dist/main.js --name ott-backend
                pm2 save
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs above.'
        }
    }
}
