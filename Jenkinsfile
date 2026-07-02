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

        stage('Docker build and push'){
            steps{
                sh'''
                aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 730558614346.dkr.ecr.ap-south-1.amazonaws.com
                docker build -t cal-webapps .
                docker tag cal-webapps:latest 730558614346.dkr.ecr.ap-south-1.amazonaws.com/cal-webapps:latest
                docker push 730558614346.dkr.ecr.ap-south-1.amazonaws.com/cal-webapps:latest
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                aws eks update-kubeconfig --name my-cluster-Jayy --region ap-south-1
                kubectl apply -f calc-deployment-svc.yaml
                kubectl get pods
                sleep 30
                kubectl get svc
                '''
            }
        }

    }
}



