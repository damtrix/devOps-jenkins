pipeline {
    agent any

    // polling by schedular
    // triggers {
    //     pollSCM('*/3 * * * *')
    // }

    environment {
        registry = 'learning/test'
        registryCredential = 'docker-hub-credentials'
        dockerImage = ''
    }

    stages {
        stage('Echo') {
            agent any
            steps {
                echo 'from github repository'
                echo 'build number is ' + "${env.BUILD_NUMBER}"
            }
        }
        // stage for cloning your github repository
        stage('Prepare') {
            agent any
            steps {
                echo 'Cloning Repository'
                
                checkout scm
            }
            post {
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }

        // gradle build
        stage('Bulid Gradle') {
            agent any
            steps {
                echo 'Bulid Gradle'
                dir ('.'){
                  sh './gradlew clean build'
                }
            }
            post {
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }

        // docker build
        stage('Bulid Docker') {
            agent any
            steps {
                echo 'Bulid Docker'
                script {
                    dockerImage = docker.build registry
                }
            }
            post {
                failure {
                  error 'This pipeline stops here...'
                }
            }
        }

        // docker push
        stage('Push Docker') {
            agent any
            steps {
                echo 'Push Docker'
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        dockerImage.push("${env.BUILD_NUMBER}") 
                        dockerImage.push('latest')
                    }
                }
            }
            post {
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }
    }
}