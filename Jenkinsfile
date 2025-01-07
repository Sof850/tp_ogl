pipeline {
    agent any

    stages {
        stage('build') {
            steps {
                script {
                    echo 'Execution des tests...'
                    sh 'chmod +x gradle'
                    sh 'gradle build'
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