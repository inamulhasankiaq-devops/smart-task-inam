pipeline {
 
    agent any
    
    tools {
        nodejs 'NodeJS'
        sonarqube 'SonarScanner'
    }

 
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
 
        S3_BUCKET = 'smart-task-management-s3-bucket'
 
        CLOUDFRONT_DISTRIBUTION_ID =
            'E2UK2ACYKOJ4OA'
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/inamulhasankiaq-devops/smart-task-inam.git'
            }
        }
 
        stage('Install') {
            steps {
                dir('frontend') {
                    sh 'npm ci'
                }
            }
        }
 
        stage('SonarQube') {
            steps {
                dir('frontend') {
 
                    withSonarQubeEnv('SonarQube') {
 
                        sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=frontend-sonar \
                        -Dsonar.projectName=frontend-sonar \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=node_modules/**,dist/**,build/**
                        '''
 
                    }
                }
            }
        }
 
        stage('Quality Gate') {
            steps {
 
                timeout(time: 10, unit: 'MINUTES') {
 
                    waitForQualityGate abortPipeline: true
 
                }
            }
        }
 
    }
}
