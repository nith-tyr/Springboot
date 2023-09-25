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
    
stage("Static Code Ananlysis") {
	steps {
		script { 
			withSonarQubeEnv(credentialsId: 'Sonar-Jenkins') {
				sh "mvn sonar:sonar"
			}
		}
	}
}
		
		stage ("Quality Gate") {
	steps {
	script { 
	waitForQualityGate abortpipeline: false, credentialsId: 'Sonar-Jenkins'
	}
	}
      }
	  
}
}
