
pipeline {
    agent any

    environment {
        // Define environment variables
        AWS_DEFAULT_REGION = 'us-east-1'
        LAMBDA_FUNCTION_NAME = 'pythonapp-deploy-jenkins'
        S3_BUCKET = 'python-app-deployment-packages'
        S3_KEY = 'pythonapp-deploy-jenkins.zip'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout the code from your source repository
                git 'https://github.com/anilradar/pythonapp-awslambda-jenkins.git'
            }
        }

        stage('Install Dependencies') {+
            steps {
                script {
                    // Install dependencies and package the application
                    sh '''
                        python3 -m venv venv
                        source venv/bin/activate
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Package Application') {
            steps {
                script {
                    // Package the application into a zip file
                    sh '''
                        zip -r9 ${LAMBDA_FUNCTION_NAME}.zip *
                    '''
                }
            }
        }

        stage('Upload to S3') {
            steps {
                script {
                    // Upload the zip file to S3
                    sh '''
                        aws s3 cp ${LAMBDA_FUNCTION_NAME}.zip s3://${S3_BUCKET}/${S3_KEY}
                    '''
                }
            }
        }

        stage('Deploy to Lambda') {
            steps {
                script {
                    // Update the Lambda function with the new code
                    sh '''
                        aws lambda update-function-code \
                        --function-name ${LAMBDA_FUNCTION_NAME} \
                        --s3-bucket ${S3_BUCKET} \
                        --s3-key ${S3_KEY}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to AWS Lambda was successful!'
        }
        failure {
            echo 'Deployment to AWS Lambda failed.'
        }
    }
}
