pipeline {
    agent any

    environment {
        MAVEN_IMAGE = 'maven:3.9.0'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.image(MAVEN_IMAGE).inside("-v /root/.m2:/root/.m2") {
                        sh 'mvn -B -DskipTests clean package'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    docker.image(MAVEN_IMAGE).inside("-v /root/.m2:/root/.m2") {
                        sh 'mvn test'
                    }
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Manual Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    docker.image(MAVEN_IMAGE).inside("-v /root/.m2:/root/.m2") {
                        sh './jenkins/scripts/deliver.sh'
                        sh 'sleep 1m'
                        
                    }
                }
            }
        }
    }
}
