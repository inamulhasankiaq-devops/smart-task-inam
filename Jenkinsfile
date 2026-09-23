pipeline {
 
    agent any

 
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
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=sqp_bc087db9f15f69768cd42891edaca950f505dfb3
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
