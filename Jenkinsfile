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
                echo "Checking out the repository..."
                // Add your actual git checkout steps here
            }
        }

        stage('Build') {
            steps {
                echo "Building the project..."
                // Add your actual build steps here (e.g., sh 'mvn clean package')
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo "Running unit and integration tests..."
                // Add your actual test steps here (e.g., sh 'mvn test')
            }
            post {
                success {
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
                // Add your actual code analysis steps here (e.g., withSonarQubeEnv('SonarQube') { sh 'mvn sonar:sonar' })
            }
        }

        stage('Security Scan') {
            steps {
                echo "Running security scan..."
                // Add your actual security scan steps here (e.g., sh 'dependency-check --project my-project --scan ./target')
            }
            post {
                success {
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
                // Add your actual deployment steps here
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo "Running integration tests on staging..."
                // Add your actual integration test steps here (e.g., sh 'selenium-side-runner -s mytests.side')
            }
        }

        stage('Deploy to Production') {
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                echo "Deploying to production server..."
                // Add your actual production deployment steps here
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

