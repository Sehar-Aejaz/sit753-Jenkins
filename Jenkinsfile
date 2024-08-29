pipeline {
    agent any

    tools {
        maven 'Maven 3.8.1'
    }

    environment {
        STAGING_SERVER = 'staging.example.com'
        PRODUCTION_SERVER = 'production.example.com'
        EMAIL_RECIPIENTS = 'seharaejaz4@gmail.com' 
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out the repository..."
                
            }
        }

        stage('Build') {
            steps {
                echo "Building the project..."
                echo " sh 'mvn clean package' "
                
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo "Running unit and integration tests..."
                echo " sh 'mvn test' "
                
            }
            post {
                success {
                    echo "Sending success email for Unit and Integration Tests..."
                    emailext(
                        subject: "Jenkins Pipeline - UNIT & INTEGRATION TESTS SUCCESS: ${env.JOB_NAME} (${env.BUILD_NUMBER})",
                        body: """
                            <p>The Unit and Integration Tests stage of pipeline <b>${env.JOB_NAME}</b> completed successfully.</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Status: <b>SUCCESS</b></p>
                            <p>Check the Jenkins console for more details.</p>
                        """,
                        to: "${env.EMAIL_RECIPIENTS}",
                        attachLog: true,
                        mimeType: 'text/html'
                    )
                }
                failure {
                    echo "Sending failure email for Unit and Integration Tests..."
                    emailext(
                        subject: "Jenkins Pipeline - UNIT & INTEGRATION TESTS FAILURE: ${env.JOB_NAME} (${env.BUILD_NUMBER})",
                        body: """
                            <p>The Unit and Integration Tests stage of pipeline <b>${env.JOB_NAME}</b> failed.</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Status: <b>FAILURE</b></p>
                            <p>Check the Jenkins console for more details.</p>
                        """,
                        to: "${env.EMAIL_RECIPIENTS}",
                        attachLog: true,
                        mimeType: 'text/html'
                    )
                }
            }
        }

        stage('Code Analysis') {
            steps {
                echo "Running code analysis..."
                echo " withSonarQubeEnv('SonarQube') { sh 'mvn sonar:sonar' } "
                
            }
        }

        stage('Security Scan') {
            steps {
                echo "Running security scan..."
                echo " sh 'dependency-check --project my-project --scan ./target' "
            }
            post {
                success {
                    echo "Sending success email for Security Scan..."
                    emailext(
                        subject: "Jenkins Pipeline - SECURITY SCAN SUCCESS: ${env.JOB_NAME} (${env.BUILD_NUMBER})",
                        body: """
                            <p>The Security Scan stage of pipeline <b>${env.JOB_NAME}</b> completed successfully.</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Status: <b>SUCCESS</b></p>
                            <p>Check the Jenkins console for more details.</p>
                        """,
                        to: "${env.EMAIL_RECIPIENTS}",
                        attachLog: true,
                        mimeType: 'text/html'
                    )
                }
                failure {
                    echo "Sending failure email for Security Scan..."
                    emailext(
                        subject: "Jenkins Pipeline - SECURITY SCAN FAILURE: ${env.JOB_NAME} (${env.BUILD_NUMBER})",
                        body: """
                            <p>The Security Scan stage of pipeline <b>${env.JOB_NAME}</b> failed.</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Status: <b>FAILURE</b></p>
                            <p>Check the Jenkins console for more details.</p>
                        """,
                        to: "${env.EMAIL_RECIPIENTS}",
                        attachLog: true,
                        mimeType: 'text/html'
                    )
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo "Deploying to staging server..."
                
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo "Running integration tests on staging..."
                echo " sh 'selenium-side-runner -s mytests.side' "
            }
        }

        stage('Deploy to Production') {
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                echo "Deploying to production server..."
                
            }
        }
    }

    post {
        always {
            echo "Cleaning up the workspace..."
            deleteDir()
        }
    }
}
