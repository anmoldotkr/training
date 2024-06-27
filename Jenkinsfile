pipeline {
    agent any

    environment {    
        // Fetch AWS session token from Jenkins credentials store
        AWS_SESSION_TOKEN = credentials('session-token')
        // Optionally fetch other credentials or environment variables
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
    }

    parameters {
        string(name: 'STACK_NAME', defaultValue: 'testing-s3withcdn-1', description: 'Enter your stack name')
        string(name: 'REGION_NAME', defaultValue: 'us-east-1', description: 'Enter Region (ex: us-east-1, us-west-2)')
        string(name: 'BUCKET_NAME', defaultValue: 'sensei0992002', description: 'S3 bucket name to upload files')
        string(name: 'VIEWER_PROTOCOL_POLICY', defaultValue: 'allow-all', description: 'Viewer protocol policy for CloudFront (allow-all, https-only, redirect-to-https)')
        string(name: 'TARGET_ORIGIN_ID', defaultValue: 'S3Origin', description: 'Target origin ID for CloudFront')
    }

    stages {
        stage('aws-authentication') {
            steps {
                // Use the environment variables for AWS authentication
                sh """
                    aws cloudformation deploy \
                    --stack-name ${params.STACK_NAME} \
                    --region ${params.REGION_NAME} \
                    --template-file ./s3WithCdn.yml \
                    --parameter-overrides \
                        bucketName=${params.BUCKET_NAME} \
                        viewerProtocolPolicy=${params.VIEWER_PROTOCOL_POLICY} \
                        targetOriginId=${params.TARGET_ORIGIN_ID} \
                    --session-token ${env.AWS_SESSION_TOKEN} \
                    --access-key ${env.AWS_ACCESS_KEY_ID} \
                    --secret-key ${env.AWS_SECRET_ACCESS_KEY}
                """
            }
        }

        stage('upload to s3') {
            steps {
                // Use the environment variables for AWS authentication
                sh "aws s3 cp ./ s3://${params.BUCKET_NAME}/ --recursive --session-token ${env.AWS_SESSION_TOKEN} --access-key ${env.AWS_ACCESS_KEY_ID} --secret-key ${env.AWS_SECRET_ACCESS_KEY}"
            }
        }
    }

    post {
        success {
            echo 'CloudFormation stack creation/update was successful!'
        }
        failure {
            echo 'CloudFormation stack creation/update failed.'
        }
    }
}


---------------------------------------------This is my code---------------------------------------------------------




pipeline {
    agent any

    environment {    
        AWS_SESSION_TOKEN = credentials('session-token')
    }

    parameters {
        string(name: 'STACK_NAME', description: 'Enter your stack name')
        string(name: 'REGION_NAME', description: 'Enter Region (ex: us-east-1, us-west-2)')
        string(name: 'BUCKET_NAME', description: 'S3 bucket name to upload files')
        
    }

    stages {
        stage('aws-authentication') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh """
                        aws cloudformation deploy \
                        --stack-name ${params.STACK_NAME} \
                        --region ${params.REGION_NAME} \
                        --parameter-overrides bucketName ${params.BUCKET_NAME} \
                        --template-file ./s3WithCdn.yml
                    """
                }
            }
        }
        
        stage('upload to s3') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh "aws s3 cp ./ s3://${params.BUCKET_NAME}/"
                }
            }
        }
    }
    
    post {
        success {
            echo 'CloudFormation stack creation/update was successful!'
        }
        failure {
            echo 'CloudFormation stack creation/update failed.'
        }
    }
}


