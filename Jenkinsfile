node {
    def dockerImage

    try {
        // Stage 1: Checkout Source Code
        stage('Checkout') {
            checkout scm
        }

        // Stage 2: Build
        stage('Build') {
            dockerImage = docker.image('maven:3.9.0').run("-v /root/.m2:/root/.m2")
            dockerImage.inside {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        // Stage 3: Test
        stage('Test') {
            dockerImage.inside {
                sh 'mvn test'
            }
        }

        // Stage 4: Post-Test Actions
        stage('Post-Test') {
            dockerImage.inside {
                junit 'target/surefire-reports/*.xml'
            }
        }

        // Stage 5: Deliver
        stage('Deliver') {
            sh './jenkins/scripts/deliver.sh'
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        if (dockerImage != null) {
            dockerImage.stop()
        }
    }
}
