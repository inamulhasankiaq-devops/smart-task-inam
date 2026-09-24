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
              script {
                def scannerHome = tool 'SonarScanner'
 
                withSonarQubeEnv('SonarQube') {
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=frontend-sonar \
                        -Dsonar.projectName=frontend-sonar \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=node_modules/**,dist/**,build/**
                    """
                }
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
     stage('Build') {
            steps {
                dir('frontend') {
                    sh 'npm run build'
                }
            }
        }
     
     stage('Deploy S3') {
            steps {
 
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-credentials',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                    )
                ]) {
 
                    dir('frontend') {
 
                        sh '''
                        set -e
                        echo "Testing AWS credentials.."
                        aws sts get-caller-identity
                        echo "Uploading to s3.."
                        aws s3 sync dist/ \
                        s3://$S3_BUCKET/ \
                        --delete
                        '''
 
                    }
                }
            }
        }
     stage('CloudFront') {
            steps {
 
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-credentials',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                    )
                ]) {
 
                    sh '''
                    aws cloudfront create-invalidation \
                    --distribution-id $CLOUDFRONT_DISTRIBUTION_ID \
                    --paths "/*"
                    '''
 
                }
            }
        }
 
    }
}
