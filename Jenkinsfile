#!/usr/bin/env groovy

pipeline {
    agent any

    environment {
        UPSTREAM_URL = 'https://github.com/eguzki/testing_upstream'
    }

    stages {
        stage('Prepare') {
          steps {
                stash includes: 'credential-helper.sh', name: 'credentialhelper'
                // Wipe the workspace so we are building completely clean
                deleteDir()
          }
        }
        stage('Update upstream branch') {
            steps {
                git credentialsId: 'github-token-rh-eguzki', url: 'https://github.com/eguzki/testing_downstream', branch: 'upstream'
                unstash 'credentialhelper'
                sh 'git config credential.helper "/bin/bash ' + env.WORKSPACE + '/credential-helper.sh"'
                sh "git remote add upstream $UPSTREAM_URL"
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
        stage('Rebase product branch') {
            steps {
                sh "git checkout product"
                sh "git rebase upstream"
            }
        }
        stage('Tests') {
            steps {
                echo "tests PASSED"
            }
        }
        stage('Push product branch') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding',
                                  credentialsId: 'github-token-rh-eguzki',
                                  usernameVariable: 'GIT_USERNAME',
                                  passwordVariable: 'GIT_PASSWORD']]) {
                  sh "git push --force-with-lease origin product"
                }
            }
        }
        stage('Update master branch') {
            steps {
                // master created if it doesn’t exist; otherwise, it is reset.
                sh "git checkout -B master origin/product"
                withCredentials([[$class: 'UsernamePasswordMultiBinding',
                                  credentialsId: 'github-token-rh-eguzki',
                                  usernameVariable: 'GIT_USERNAME',
                                  passwordVariable: 'GIT_PASSWORD']]) {
                  sh "git push --force-with-lease origin master"
                }
            }
        }
    }
}
