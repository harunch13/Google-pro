node{
   stage('1.Git Build') {
      git branch: 'main', url: 'https://github.com/harunch13/Google-pro.git'
      }  
   stage('2.Maven Build') {
      withMaven(maven: 'maven3.9.16') {
      sh 'mvn clean package -Dmaven.test.skip=true'
      }
   }
   stage('3.Sonarqube Analysis') {
      withCredentials([string(credentialsId: 'sonar-google', variable: 'SONAR_TOKEN')]) {
            withMaven(maven: 'maven3.9.16') {
              sh "mvn sonar:sonar -Dsonar.host.url=http://sonar:9000 -Dsonar.login=$SONAR_TOKEN"
         }  
      }
   }
} 
