pipeline {
    agent any

    tools {
        maven 'Maven 3.8.1'
    }

    environment {
        STAGING_SERVER = 'staging.example.com'
        PRODUCTION_SERVER = 'production.example.com'
        EMAIL_RECIPIENTS = 'seharaejaz@.com' // Set your email recipients here
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Sehar-Aejaz/sit753-Jenkins.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                success {
                    sendEmail('Unit and Integration Tests', 'SUCCESS')
                }
                failure {
                    sendEmail('Unit and Integration Tests', 'FAILURE')
                    error 'Unit and Integration Tests failed'
                }
            }
        }

        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Security Scan') {
            steps {
                sh 'dependency-check --project my-project --scan ./target'
            }
            post {
                success {
                    sendEmail('Security Scan', 'SUCCESS')
                }
                failure {
                    sendEmail('Security Scan', 'FAILURE')
                    error 'Security Scan failed'
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh '''
                    scp target/myapp.jar user@$STAGING_SERVER:/path/to/deploy/
                    ssh user@$STAGING_SERVER "java -jar /path/to/deploy/myapp.jar"
                '''
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                sh 'selenium-side-runner -s mytests.side'
                sh 'newman run myapi.postman_collection.json'
            }
        }

        stage('Deploy to Production') {
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                sh '''
                    scp target/myapp.jar user@$PRODUCTION_SERVER:/path/to/deploy/
                    ssh user@$PRODUCTION_SERVER "java -jar /path/to/deploy/myapp.jar"
                '''
            }
        }
    }

    post {
        always {
            deleteDir()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Notifying team...'
        }
    }
}

def sendEmail(stageName, status) {
    emailext (
        subject: "${stageName} - ${status}",
        body: """
            <p>The ${stageName} stage has completed with status: <b>${status}</b>.</p>
            <p>Check the attached log for more details.</p>
        """,
        recipientProviders: [[$class: 'DevelopersRecipientProvider']],
        to: "${env.EMAIL_RECIPIENTS}",
        attachLog: true,
        mimeType: 'text/html'
    )
}
