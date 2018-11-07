pipeline {
    agent any 
    stages {
        stage('Stage 1') {
            steps {
                git credentialsId: 'github-token-rh-eguzki', url: 'https://github.com/3scale/backend'
                echo 'Hello world!' 
                sh "git status"
                sh "ls -al"
            }
        }
    }
}
