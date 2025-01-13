pipeline {
    agent any
    tools {
        jdk 'Java 17'
        gradle 'Gradle_Latest'
    }
    stages {
        stage('Test') {
            steps {
                script {
                    bat 'gradlew clean test'

                    junit '**/build/test-results/**/*.xml'

                    bat 'gradlew cucumberReport'
                }
            }
        }

        stage('Code Analysis') {
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        bat 'gradlew sonarqube'
                    }
                }
            }
        }

        stage('Code Quality') {
            steps {
                script {
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Quality Gate failed, stopping the pipeline."
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    bat 'gradlew clean build'

                    bat 'gradlew javadoc'

                    archiveArtifacts artifacts: '**/build/libs/*.jar, **/build/docs/**/*', fingerprint: true
                }
            }
        }

    }
    post {
        always {
            echo "Pipeline completed."
        }
    }
}
