pipeline {
     agent any

     stages{
           // stage 1. Git Build
           stage('1.Git Build') {
               steps{
                  git branch: 'main', url: 'https://github.com/harunch13/Google-pro.git'
                }
            }

           // stage 2. Maven Build
            stage('2.Maven Build') {
                steps {
                    withMaven(maven: 'maven3.9.16') {
                     sh 'mvn clean package -Dmaven.test.skip=true'
                    }
                }
            }

            // stage 3. Sonarqube Analysis
            stage('3.Sonarqube Analysis') {
                steps {
                    echo "✅Skipping SonarQube analysis"
                }
            }

            // stage 4. Deploy to Nexus 
            stage('4.Deploy to Nexus') {
                steps {
                     echo "✅Skipping Nexus deployment"
                }
            }

            // stage 5. Deploy to Tomcat
            stage('5.Deploy to Tomcat') {
                steps {
                     echo"🚀 Deploying WAR to Tomcat via manager API..."
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

      // post-buld actions
      post {
           success {
                echo "✅ Build completed successfully!" 
           }
           failure {
                echo "❌ Build failed-check console output for details"
           }
      }
}
