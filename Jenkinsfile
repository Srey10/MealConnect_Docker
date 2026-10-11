// Testing Jenkins automatic deployment
pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        PATH = "/Applications/Docker.app/Contents/Resources/bin:/opt/homebrew/bin:/usr/local/bin:${env.PATH}"
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        
        stage('Check Docker') {
            steps {
                sh '''
                    export PATH="/Applications/Docker.app/Contents/Resources/bin:/opt/homebrew/bin:/usr/local/bin:$PATH"
                    docker --version
                    docker compose version
                    docker info
                '''
            }
        }


        stage('Validate Compose') {
            steps {
                sh 'docker compose config -q'
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d --remove-orphans'
            }
        }

        stage('Verify') {
            steps {
                sh 'docker compose ps'
            }
        }
    }

    post {
        success {
            echo 'MealConnect deployment completed.'
        }
        failure {
            echo 'Deployment failed. Check Console Output.'
        }
    }
}
