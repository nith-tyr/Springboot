pipeline {
  agent { label 'Jenkins-Agent' }
  tools {
    jdk 'Java17'
    maven 'Maven3'
  }
  stages {
    stage("Clean Workspace") {
      steps{
        cleanWs()
      }
    }
     stage ("Checkout for SCM") {
       steps {
         git branch : 'main' , credentialsId: 'github', url: 'https://github.com/nith-tyr/Springboot.git'
           }
     }

    stage('Build and Test') {
      steps {
        sh 'ls -ltr'
        // build the project and create a JAR file
        sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn clean package'
      }
    }
    
stage('Static Code Analysis') {
      environment {
        SONAR_URL = "http://http://54.199.15.159:9000/"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'Sonar-Jenkins')]) {
          sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn sonar:sonar -Dsonar.login=$Sonar-Jenkins -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }

    stage ("Qualtiy Gate") {
	steps {
	script { 
	waitForQualityGate abortpipeline: false, credentialsId: 'Sonar-Jenkins'
	}
	}
      
  }
}

