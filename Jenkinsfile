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
                             subject: "Succes de la construction : ",
                             body: ":rocket: *Deploiement termine avec succes!* :tada:"
                        slackSend channel: '#tp-ogl',
                            color: 'good',
                            message: ':rocket: *Deploiement termine avec succes!* :tada:'
                    }
                    else {
                        echo 'Envoi de notifications d’échec...'
                        mail to: 'ks_chabi@esi.dz',
                             subject: "Echec de la construction : ",
                             body: "La construction a echoue. Consultez les journaux pour plus de details."
                        slackSend channel: '#tp-ogl',
                            color: 'danger',
                            message: ':sob: *Deploiement echoue...* :angry:'
                    }
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
