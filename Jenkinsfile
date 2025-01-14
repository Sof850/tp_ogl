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

                }
            }
            post {
                always {
                    cucumber '**/reports/*.json'
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

        stage('Deploy') {
            steps {
                 script {
                    echo 'Deploiement de lapplication...'
                    bat 'gradlew publish'
                 }
            }
        }

        stage('Notification') {
            steps {
                script {
                    currentBuild.result = currentBuild.result ?: 'SUCCESS'
                    if (currentBuild.result == 'SUCCESS') {
                        echo 'Envoi de notifications de succes...'
                        mail to: 'ks_chabi@esi.dz',
                             subject: "Successful Deployment: ",
                             body: "Successful deployment!"
                        slackSend channel: '#tp-ogl',
                            color: 'good',
                            message: ':rocket: *Successful deployment!* :tada:'
                    }
                    else {
                        echo 'Envoi de notifications d’échec...'
                        mail to: 'ks_chabi@esi.dz',
                             subject: "Error in building : ",
                             body: "Build error, please check logs."
                        slackSend channel: '#tp-ogl',
                            color: 'danger',
                            message: ':sob: *Unsuccessful deployment...* :angry:'
                    }
                }
            }
        }

        stage('notifySlack') {
                    steps {
                        slackSend channel: '#tp-ogl',
                            color: 'good',
                            message: ':rocket: *Successful deployment!* :tada:'
                    }
                }



    }
    post {
        always {
            echo "Pipeline completed."
        }
    }
}
