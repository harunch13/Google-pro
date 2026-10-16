pipeline {
    agent any

    environment {
        WAR_FILE = 'target/linkpay.war'
        MAVEN_TOOL = 'maven3.10.0'
        SLACK_CHANNEL = '#sam'
        EC2_IP = '13.51.168.188'
        EC2_USER = 'ubuntu'
    }

    stages {
        stage('1. Git Build') {
            steps {
                git branch: 'main', url: 'https://github.com/harunch13/google-pro.git'
            }
        }

        stage('2. Maven Build') {
            steps {
                withMaven(maven: 'maven3.10.0') {
                    sh "mvn clean package -Dmaven.test.skip=true"
                }
            }
        }

        stage('3. SonarQube Analysis') {
            steps {
                echo "⏩ Skipping SonarQube analysis for now..."
            }
        }

        stage('4. Deploy to Nexus') {
            steps {
                echo "⏩ Skipping Nexus deployment for now..."
            }
        }

        stage('5. Deploy to Test Tomcat') {
            steps {
                echo "🚀 Deploying WAR to TEST Tomcat server..."
                deploy adapters: [
                    tomcat9(
                        credentialsId: 'google-deploy',
                        url: 'http://tomcat:8080/'
                    )
                ],
                contextPath: 'linkpay-test',
                war: 'target/linkpay.war'
            }
        }

        stage('6. Manual Approval Before Production') {
            steps {
                script {
                    slackSend(
                        channel: "${SLACK_CHANNEL}",
                        message: "🟡 *AWAITING APPROVAL:* ${env.JOB_NAME} #${env.BUILD_NUMBER} — Waiting for manual approval to deploy to *PRODUCTION* EC2 ${EC2_IP}.",
                        color: '#e6c300'
                    )
                    timeout(time: 10, unit: 'MINUTES') {
                        input message: "✅ Approve deployment to PRODUCTION EC2 ${EC2_IP}?"
                    }
                    slackSend(
                        channel: "${SLACK_CHANNEL}",
                        message: "🟢 *APPROVED:* ${env.JOB_NAME} #${env.BUILD_NUMBER} — Proceeding with *PRODUCTION* deployment to ${EC2_IP}.",
                        color: 'good'
                    )
                }
            }
        }

        // FIXED STAGE 7 - Ubuntu 26.04 Tomcat10
        stage('7. Deploy to Production EC2') {
            steps {
                echo "🚀 Deploying WAR to PRODUCTION EC2 ${EC2_IP}..."
                sshagent(['ec2-deploy-key']) {
                    sh """
                        echo "Copying WAR to EC2..."
                        scp -o StrictHostKeyChecking=no target/linkpay.war ${EC2_USER}@${EC2_IP}:/home/${EC2_USER}/linkpay.war
                        
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} "
                            echo 'Deploying to Tomcat10...'
                            sudo mkdir -p /var/lib/tomcat10/webapps
                            sudo cp /home/${EC2_USER}/linkpay.war /var/lib/tomcat10/webapps/linkpay.war
                            sudo chown tomcat:tomcat /var/lib/tomcat10/webapps/linkpay.war
                            sudo chmod 644 /var/lib/tomcat10/webapps/linkpay.war
                            sudo systemctl restart tomcat10
                            sleep 7
                            echo 'Deployment SUCCESS!'
                            ls -lh /var/lib/tomcat10/webapps/linkpay.war
                            sudo systemctl status tomcat10 --no-pager | head -15
                        "
                    """
                }
            }
        }
    }

    post {
        success {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                message: "✅ *SUCCESS:* ${env.JOB_NAME} #${env.BUILD_NUMBER} — Deployed to EC2 ${EC2_IP}:8080/linkpay",
                color: 'good'
            )
        }
        failure {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                message: "❌ *FAILED:* ${env.JOB_NAME} #${env.BUILD_NUMBER} — Check Jenkins logs.",
                color: 'danger'
            )
        }
        unstable {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                message: "⚠️ *UNSTABLE:* ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                color: 'warning'
            )
        }
    }
}
