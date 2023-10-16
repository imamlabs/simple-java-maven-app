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
        script {
            docker.image(mavenImage).inside('-v /root/.m2:/root/.m2') {
                input message: 'Lanjutkan ke tahap Deploy?'
            }
        }
    }
    }

    stage('Deploy') {
        docker.image(mavenImage).inside('-v /root/.m2:/root/.m2') {
            sh './jenkins/scripts/deliver.sh'
            // User bisa melakukan test App's selama se-menit
            sh 'sleep 1m'
            
        }
    }
}
