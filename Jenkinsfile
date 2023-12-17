pipeline {
    agent { label 'jenkins-agent' }
    tools {
        jdk 'java17'
        maven 'maven3'
    }
   stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'master', credentialsId: 'github', url: 'https://github.com/sagar2390/java-hello-world'
                }
        }
       stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }
       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
          stage("SonarQube analysis") {
            steps {
            script {
              withSonarQubeEnv(credentialsId:'jenkins-sonarqube-token') {
                sh 'mvn sonar:sonar'
              }
          }
      }
   }
         stage("Quality Gate") {
            steps {
            script {
                waitForQualityGate abortPipeline: false, credentialsId:'jenkins-sonarqube-token'
              }
         }
     }
  }
}
