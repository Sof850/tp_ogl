pipeline {
    agent any
    tools {
        jdk 'Java 17'
        gradle 'Gradle_Latest'
    }
    stages {
        stage('Build') {
            steps {
                bat 'gradlew clean build'
            }
        }
        stage('Test') {
            steps {
                bat 'gradlew test'
            }
        }
        stage('Package') {
            steps {
                // Package the application
                bat 'gradlew assemble'
            }
        }
    }
    post {
        always {
            // Archive the build artifacts
            archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
        }
        success {
            echo 'Build and tests completed successfully!'
        }
        failure {
            echo 'Build or tests failed. Check the logs!'
        }
    }
}
