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

    stage('Deliver') {
        docker.image(mavenImage).inside('-v /root/.m2:/root/.m2') {
            sh './jenkins/scripts/deliver.sh'
        }
    }
}
