#!/usr/bin/env groovy
pipeline {
    agent any
    stages {
        stage('Source Code Management') {
            steps {
                script {
                    slackSend color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
                    try {
                        git credentialsId: '3371b594-48bb-48fa-accd-5c01e26f1dc2', url: 'https://captivators-shreyas@bitbucket.org/deloitte-captivators/captivators-hackathon.git'
                    }
                    catch (exc) {
                        slackSend color: '#FF0000', message: 'Error in fetching GIT Repo: \n'+exc
                        emailext body: ''+exc, subject: 'Angular2DevOps: Build Failure', to: 'duke.shreyas@gmail.com'
                        throw exc
                    }
                }
            }
        }
		
		stage('SonarQube analysis') {
            steps {
                script {
                    slackSend color: '008000', message: 'GIT PULL SUCCESSFUL'
                    try {
						build 'sonarQube'
                    }
                    catch (exc) {
                        slackSend color: '#439FE0', message: 'Error in SonarQube analysis \n'+exc
                        emailext body: ''+exc, subject: 'Angular2DevOps: Build Failure', to: 'duke.shreyas@gmail.com'
                        throw exc
                    }
                }
            }
        }
		
		stage('Installing packages') {
            steps {
                script {
                    slackSend color: '008000', message: 'SONARQUBE ANALYSIS SUCCESSFUL'
                    try {
						bat 'npm install'
                    }
                    catch (exc) {
                        slackSend color: '#439FE0', message: 'Error while installing packages \n'+exc
                        emailext body: ''+exc, subject: 'Angular2DevOps: Build Failure', to: 'duke.shreyas@gmail.com'
                        throw exc
                    }
                }
            }
        }
		
		stage('Unit test') {
            steps {
                script {
                    slackSend color: '008000', message: 'PACKAGES INSTALLATION SUCCESSFUL'
                    try {
						bat 'npm test'
                    }
                    catch (exc) {
                        slackSend color: '#439FE0', message: 'Error while unit teting \n'+exc
                        emailext body: ''+exc, subject: 'Angular2DevOps: Build Failure', to: 'duke.shreyas@gmail.com'
                        throw exc
                    }
                }
            }
        }
                
        stage('E2E test') {
            steps {
                script {
                    slackSend color: '008000', message: 'UNIT TESTING SUCCESSFUL'
                    try {
						bat 'npm run e2e'
                    }
                    catch (exc) {
                        slackSend color: '#439FE0', message: 'Error while e2e testing \n'+exc
                        emailext body: ''+exc, subject: 'Angular2DevOps: Build Failure', to: 'duke.shreyas@gmail.com'
                        throw exc
                    }
                }
            }
        }

		stage('Build') {
            steps {
                script {
                    slackSend color: '008000', message: 'E2E TESTING SUCCESSFUL'
                    try {
						bat 'npm run jenkins:build'
                    }
                    catch (exc) {
                        slackSend color: '#439FE0', message: 'Error while running build \n'+exc
                        emailext body: ''+exc, subject: 'Angular2DevOps: Build Failure', to: 'duke.shreyas@gmail.com'
                        throw exc
                    }
                }
            }
        }	

		stage('Deploy') {
            steps {
                script {
                    slackSend color: '008000', message: 'BUILD SUCCESSFUL'
                    try {
						bat 'XCOPY "./dist" "C:\\xampp\\tomcat\\webapps\\angular2devops" /E /Y'
                    }
                    catch (exc) {
                        slackSend color: '#439FE0', message: 'Error while deploying \n'+exc
                        emailext body: ''+exc, subject: 'Angular2DevOps: Build Failure', to: 'duke.shreyas@gmail.com'
                        throw exc
                    }
                }
            }
        }			
    }
    post {
        success {
            slackSend color: '008000', message: 'DEPLOYMENT SUCCESSFUL'
            emailext body: '', subject: 'Angular2DevOps: Deployment Successful', to: 'duke.shreyas@gmail.com'
        }
        failure {
            slackSend color: '#439FE0', message: 'DEPLOYMENT FAILURE'
            emailext body: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'+exc, subject: 'Angular2DevOps: Deployment Failure', to: 'duke.shreyas@gmail.com'
        }
    }
}
