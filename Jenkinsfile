node {
    def mavenImage = 'maven:3.9.0'
    
    stage('Build') {
        docker.image(mavenImage).inside('-v /root/.m2:/root/.m2') {
            sh 'mvn -B -DskipTests clean package'
        }
    }

    stage('Test') {
        docker.image(mavenImage).inside('-v /root/.m2:/root/.m2') {
            sh 'mvn test'
        }
        junit 'target/surefire-reports/*.xml'
    }
    stage('Manual Approval') {
            steps {
                // Menunggu persetujuan manual
                input message: 'Lanjutkan ke tahap Deploy?'
            }
        }

    stage('Deploy') {
        docker.image(mavenImage).inside('-v /root/.m2:/root/.m2') {
            sh './jenkins/scripts/deliver.sh'
            // User bisa melakukan test App's selama se-menit
            sh 'sleep 1m'
            //sehubung tdk menemukan file untuk menghentikan proses App pada master saya buat sendiri lngsung d jenkinfile
            def jarName = "${NAME}-${VERSION}.jar"
            sh "pgrep -f 'java -jar $jarName' > .pidfile" //idtfikasi PID java
            def pidFile = readFile('.pidfile').trim() // Mmbaca PID dari berkas .pidfile
            if (pidFile) {
                sh "kill $pidFile" // Menghentikan proses Java berdasarkan PID jika PID ditemukan
            } else {
                echo "Tidak ada proses Java yang berjalan dengan nama $jarName"
            }
        }
    }
}
