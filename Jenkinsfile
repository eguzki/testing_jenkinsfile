#!/usr/bin/env groovy

pipeline {
    agent any

    environment {
        UPSTREAM_URL = 'https://github.com/eguzki/testing_upstream'
    }

    stages {
        stage('Update upstream branch') {
            steps {
                stash includes: 'credential-helper.sh', name: 'credentialhelper'
                // Wipe the workspace so we are building completely clean
                deleteDir()
                git credentialsId: 'github-token-rh-eguzki', url: 'https://github.com/eguzki/testing_downstream', branch: 'upstream'
                unstash 'credentialhelper'
                sh 'git config credential.helper "/bin/bash ' + env.WORKSPACE + '/credential-helper.sh"'
                sh "git remote -vv"
                sh "git branch -vv"
                sh "git remote add upstream $UPSTREAM_URL"
                sh "git remote -vv"
                sh "git fetch upstream"
                sh "git pull upstream master"
                withCredentials([[$class: 'UsernamePasswordMultiBinding',
                                  credentialsId: 'github-token-rh-eguzki',
                                  usernameVariable: 'GIT_USERNAME',
                                  passwordVariable: 'GIT_PASSWORD']]) {
                  sh "git push origin upstream"
                }
            }
        }
    }
}
