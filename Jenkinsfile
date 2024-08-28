pipeline {
    agent any

    tools {
        maven 'Maven 3.8.1'
    }

    environment {
        STAGING_SERVER = 'staging.example.com'
        PRODUCTION_SERVER = 'production.example.com'
        EMAIL_RECIPIENTS = 'seharaejaz4@gmail.com' // Set your email recipients here
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Sehar-Aejaz/sit753-Jenkins.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo "sh 'mvn clean package'"
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo "sh 'mvn test'"
            }
            post {
                success {
                    script {
                        sendEmail('Unit and Integration Tests', 'SUCCESS')
                    }
                }
                failure {
                    script {
                        sendEmail('Unit and Integration Tests', 'FAILURE')
                    }
                    echo "error 'Unit and Integration Tests failed'"
                }
            }
        }

        stage('Code Analysis') {
            steps {
                echo "withSonarQubeEnv('SonarQube') { sh 'mvn sonar:sonar' }"
            }
        }

        stage('Security Scan') {
            steps {
                echo "sh 'dependency-check --project my-project --scan ./target'"
            }
            post {
                success {
                    script {
                        sendEmail('Security Scan', 'SUCCESS')
                    }
                }
                failure {
                    script {
                        sendEmail('Security Scan', 'FAILURE')
                    }
                    echo "error 'Security Scan failed'"
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo '''
                    scp target/myapp.jar user@$STAGING_SERVER:/path/to/deploy/
                    ssh user@$STAGING_SERVER "java -jar /path/to/deploy/myapp.jar"
                '''
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo "sh 'selenium-side-runner -s mytests.side'"
                echo "sh 'newman run myapi.postman_collection.json'"
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "input message: 'Deploy to production?', ok: 'Deploy'"
                echo '''
                    scp target/myapp.jar user@$PRODUCTION_SERVER:/path/to/deploy/
                    ssh user@$PRODUCTION_SERVER "java -jar /path/to/deploy/myapp.jar"
                '''
            }
        }
    }

    post {
        always {
            echo "deleteDir()"
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Notifying team..."
            script {
                sendEmail('Pipeline Failure', 'FAILURE')
            }
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
        to: "${EMAIL_RECIPIENTS}",
        attachLog: true,
        mimeType: 'text/html'
    )
}



