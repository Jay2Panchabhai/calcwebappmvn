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

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        try {
                            def qg = waitForQualityGate()
                            echo "Quality Gate Status: ${qg.status}"
                            if (qg.status != 'OK') {
                                error "Quality Gate failed: ${qg.status}"
                            }
                        } catch (Exception e) {
                            echo "Quality Gate check failed: ${e.message}"
                            error "Quality Gate stage failed"
                        }
                    }
                }
            }
        }

        stage('SonarQube Report') {
            steps {
                script {
                    echo "SonarQube Report generated Doneeee."
                }
            }
        }

    }
}



