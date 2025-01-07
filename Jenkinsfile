pipeline {
    agent any

    environment {
        MAVEN_REPO_USERNAME = "myMavenRepo"
        MAVEN_REPO_PASSWORD = "azerty"
    }

    stages {
        // Étape pour exécuter les tests
        stage('test') {
            steps {
                script {
                    echo 'Execution des tests...'
                    sh 'chmod +x gradle'
                    sh 'gradle test'
                }
            }
            post {
                always {
                    cucumber '**/reports/*.json'
                }
            }
        }

        // Étape pour l'analyse de code (SonarQube)
        stage('codeAnalysis') {
            steps {
                script {
                    echo 'Execution de lanalyse SonarQube...'
                    withSonarQubeEnv('sonar') {
                        sh 'gradle sonar'
                    }
                }
            }
        }

        // Étape pour vérifier la qualité du code (SonarQube Quality Gate)
        stage('codeQuality') {
            steps {
                script {
                    echo 'Verification du Quality Gate de SonarQube...'
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Le Quality Gate de SonarQube a echoue : ${qualityGate.status}"
                    }
                }
            }
        }

        // Étape pour construire le projet
        stage('build') {
            steps {
                script {
                    echo 'Construction du projet...'
                    sh 'gradle build'
                }
            }
        }

        // Étape pour archiver les artefacts
        stage('archiveArtifacts') {
            steps {
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                archiveArtifacts artifacts: 'build/reports/tests/**/*', fingerprint: true
                archiveArtifacts artifacts: 'build/reports/cucumber/**/*', fingerprint: true
            }
        }

        // Étape pour déployer l'application
        stage('deploy') {
            steps {
                script {
                    echo 'Deploiement de lapplication...'
                    sh 'gradle publish'
                }
            }
        }

        // Étape pour envoyer des notifications par e-mail
        stage('notifyMail') {
            steps {
                script {
                    currentBuild.result = currentBuild.result ?: 'SUCCESS'
                    if (currentBuild.result == 'SUCCESS') {
                        echo 'Envoi de notifications de succes...'
                        mail to: 'ks_chabi@esi.dz',
                             subject: "Succes de la construction : ",
                             body: ":rocket: *Deploiement termine avec succes!* :tada:"
                    } else {
                        echo 'Envoi de notifications d’échec...'
                        mail to: 'raouf.selidja@gmail.com',
                             subject: "Echec de la construction : ",
                             body: "La construction a echoue. Consultez les journaux pour plus de details."
                    }
                }
            }
        }

        // Étape pour envoyer des notifications avec Slack
        stage('notifySlack') {
            steps {
                slackSend channel: '#tp-ogl',
                    color: 'good',
                    message: ':rocket: *Deploiement termine avec succes!* :tada:'
            }
        }
    }

    post {
        always {
            echo 'Pipeline termine.'
        }
        success {
            echo 'Pipeline complete avec succes.'
        }
        failure {
            echo 'Le pipeline a echoue.'
        }
    }
}