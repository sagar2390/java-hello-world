pipeline {
    agent { label 'jenkins-agent' }
    tools {
        jdk 'java17'
        maven 'maven3'
    }
    environment {
	    APP_NAME = "java-hello-world"
            RELEASE = "1.0.0"
            DOCKER_USER = "docker2390"
            DOCKER_PASS = 'Mydocker@123'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }

       }
  }
}
