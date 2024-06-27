
// ---------------------------------------------This is my code---------------------------------------------------

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


