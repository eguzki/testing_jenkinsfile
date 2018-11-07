#!/usr/bin/env groovy

pipeline {
    agent any

    def githubCredentialsId = 'github-token-rh-eguzki'

    environment {
        UPSTREAM_URL = 'https://github.com/eguzki/testing_upstream'
    }

    stages {
        stage('Update upstream branch') {
            steps {
                git credentialsId: githubCredentialsId, url: 'https://github.com/eguzki/testing_downstream', branch: 'upstream'
                sh "git remote add upstream $UPSTREAM_URL"
                sh "git remote -vv"
                sh "git fetch upstream"
                sh "git pull upstream master"
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'github-token-rh-eguzki', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD']]) {
                  sh "git push https://${env.GIT_USERNAME}:${env.GIT_PASSWORD}@github.com/eguzki/testing_downstream upstream"
                }
            }
        }
    }
}
