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
                        //sehubung tdk menemukan file untuk menghentikan proses App pada master saya buat sendiri lngsung d jenkinfile
                        def jarName = "my-app-1.0-SNAPSHOT.jar"
                        sh pgrep -f "java -jar my-app-1.0-SNAPSHOT.jar > .pidfile"
                       // sh "pgrep -f 'java -jar $jarName' > .pidfile" //idtfikasi PID java
                        def pidFile = readFile('.pidfile').trim() // Mmbaca PID dari berkas .pidfile
                        if (pidFile) {
                            sh "kill $pidFile" // Menghentikan proses Java berdasarkan PID jika PID ditemukan
                        } else {
                            echo "Tidak ada proses Java yang berjalan dengan nama $jarName"
                        }
                    }
                }
            }
        }
    }
}
