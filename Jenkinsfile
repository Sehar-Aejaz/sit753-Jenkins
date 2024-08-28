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
                echo "git url: 'https://github.com/Sehar-Aejaz/sit753-Jenkins.git', branch: 'main'"
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
        success {
            emailext(
                subject: "Jenkins Pipeline - SUCCESS: ${env.JOB_NAME} (${env.BUILD_NUMBER})",
                body: """
                    <p>The pipeline <b>${env.JOB_NAME}</b> completed successfully.</p>
                    <p>Build Number: ${env.BUILD_NUMBER}</p>
                    <p>Check the Jenkins console for more details.</p>
                """,
                to: "${env.EMAIL_RECIPIENTS}",
                attachLog: true,
                mimeType: 'text/html'
            )
        }
        failure {
            emailext(
                subject: "Jenkins Pipeline - FAILURE: ${env.JOB_NAME} (${env.BUILD_NUMBER})",
                body: """
                    <p>The pipeline <b>${env.JOB_NAME}</b> has failed.</p>
                    <p>Build Number: ${env.BUILD_NUMBER}</p>
                    <p>Check the Jenkins console for more details.</p>
                """,
                to: "${env.EMAIL_RECIPIENTS}",
                attachLog: true,
                mimeType: 'text/html'
            )
        }
        always {
            echo "Cleaning up the workspace..."
            deleteDir()
        }
    }
}
