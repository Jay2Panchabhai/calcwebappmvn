pipeline {
    agent any

    tools {
        maven 'Maven1'
    }

    environment {
        my_aws_credential = credentials('aws-access')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'cal-pipeline', url: 'https://github.com/Jay2Panchabhai/calcwebappmvn.git'
                echo 'clonning is successful'
            }
        }

        stage('Build') {
            steps {
                sh '''
                ls -la
                mvn clean package
                ls -la
                '''
            }

            post {
                success {
                    archiveArtifacts 'target/*.war'
                    sh 'ls -la'
                }
            }
        }


        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }
}



