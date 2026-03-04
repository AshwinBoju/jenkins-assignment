pipeline {
    agent { label 'maven-agent' }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh '''
                scp target/*.jar ubuntu@100.55.83.147:/home/ubuntu/
                '''
            }
        }
    }
}
