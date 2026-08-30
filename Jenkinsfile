pipeline {
    agent any

    environment {
        WAR_FILE = 'target/linkpay.war'  // Adjust to your project WAR name
        MAVEN_TOOL = 'maven3.9.16'
    }

    stages {
        // Stage 1: Git Build
        stage('1. Git Build') {
            steps {
                git branch: 'main', url: 'https://github.com/harunch13/Google-pro.git'
            }
        }

        // Stage 2: Maven Build
        stage('2. Maven Build') {
            steps {
                withMaven(maven: 'maven3.9.16') {
                    sh "mvn clean package -Dmaven.test.skip=true"
                }
            }
        }

        // Stage 3: SonarQube Analysis (optional)
        stage('3. SonarQube Analysis') {
            steps {
                echo "⏩ Skipping SonarQube analysis for now..."
            }
        }

        // Stage 4: Deploy to Nexus (optional)
        stage('4. Deploy to Nexus') {
            steps {
                echo "⏩ Skipping Nexus deployment for now..."
            }
        }

        // ✅ Stage 5: Deploy to Test Tomcat
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

        // ✅ Stage 6: Manual Approval Before Production
        stage('6. Manual Approval Before Production') {
            steps {
                script {
                    timeout(time: 10, unit: 'MINUTES') {
                        input message: "✅ Approve deployment to PRODUCTION Tomcat?"
                    }
                }
            }
        }

        // ✅ Stage 7: Deploy to Prod Tomcat
        stage('7. Deploy to Production Tomcat') {
            steps {
                echo "🚀 Deploying WAR to PRODUCTION Tomcat server..."
                deploy adapters: [
                    tomcat9(
                        credentialsId: 'google-deploy',
                        url: 'http://tomcat:8080/'
                    )
                ],
                contextPath: 'linkpay',
                war: 'target/linkpay.war'
            }
        }
    }
}
