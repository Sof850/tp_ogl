pipeline {
    agent any

    stages {
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