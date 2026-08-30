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
         steps{
             withMaven(maven: 'maven3.9.16') {
                sh 'mvn clean package -Dmaven.test.skip=true'
             }
          }
       }

       //stage 3.Sonarqube Analysis
       stage('3.Sonarqube Analysis') {
          steps{
             echo "✅skip SonarQube, because we are done analysis"
          }
       }

       //stage 4.Deploy to Nexus
       stage('4.Deploy to Nexus') {
          steps{
              withMaven(maven: 'maven3.9.16') {
                 sh 'mvn deploy -Dmaven.test.skip=true'
              }
          }
       }
    }

    //post-build actions
    post{
       success {
           echo"✅Build completed successfully!"
        }
        failure {
           echo" ❌Build failed -- check console output for details"
        }
    }
}
