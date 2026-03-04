pipeline {
    agent { label 'maven-agent' }

    environment {
        APP_SERVER = "100.55.83.147"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Security Scan') {
            steps {
                sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL .'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
                archiveArtifacts artifacts: 'target/*.jar'
            }
        }

        stage('Deploy to Application Server') {
            when {
                branch 'main'
            }
            steps {
                sshagent(credentials: ['app-server-ssh']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no target/*.jar ubuntu@$APP_SERVER:/home/ubuntu/app.jar
                    ssh -o StrictHostKeyChecking=no ubuntu@$APP_SERVER "
                        pkill -f app.jar || true
                        nohup java -jar /home/ubuntu/app.jar > app.log 2>&1 &
                    "
                    '''
                }
            }
        }
    }
}

