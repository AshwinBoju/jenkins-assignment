pipeline {
agent { label 'maven-node' }


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
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
    }

    stage('Deploy to Application Server') {
    steps {
        sshagent(credentials: ['app-server-ssh']) {
            sh '''
            scp -o StrictHostKeyChecking=no target/maven-simple-1.0-SNAPSHOT.jar ubuntu@100.28.222.49:/home/ubuntu/app.jar

            ssh -o StrictHostKeyChecking=no ubuntu@100.28.222.49 "
                pkill -f app.jar || true
                nohup java -jar /home/ubuntu/app.jar > app.log 2>&1 &
            "
            '''
        }
    }
}

}


}



