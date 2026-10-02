pipeline {
     agent any 

     stages{
       // Stage 1.Git Build
       stage('1.Git build') {
          steps {
              git branch: 'main', url: 'https://github.com/harunch13/Google-pro.git'
            }
        }

       // Stage 2.Maven Build
       stage('2.Maven Build') {
          steps {
              withMaven(maven: 'maven3.10.0') {
                  sh 'mvn clean package -Dmaven.test.skip=true'
                }
            }
        }

       // stage 3.Sonarqube Analysis
       stage('3. Sonarqube Analysis') {
          steps {
              withCredentials([string(credentialsId: 'sonar-google', variable: 'SONAR_TOKEN')]) {
                   withMaven(maven: 'maven3.10.0') {
                       sh "mvn sonar:sonar -Dsonar.host.url=http://sonar:9000 -Dsonar.login=$SONAR_TOKEN"
                    }
                }
            }
        } 
    }
}
