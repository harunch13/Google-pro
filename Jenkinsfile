pipeline{
    agent any 

    stages{
        // stage 1.Git Build
        stage('1. Git build') {
            steps {
                git branch: 'main', url: 'https://github.com/harunch13/Google-pro.git'
            }
        }

        // stage 2.Maven Build
        stage('2. Maven Build') {
            steps {
                withMaven(maven: 'maven3.10.0') {
                    sh 'mvn clean package -Dmaven.test.skip=true'
                }
            }
        }
    }
}
