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
        SONAR_URL = "http://54.199.15.159:9000/"
      }
      steps {
        withCredentials([string(credentialsId: 'Sonar-Jenkins', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN  -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }
		
//		stage ("Quality Gate") {
//	steps {
//	script { 
//	waitForQualityGate abortpipeline: false, credentialsId: 'Sonar-Jenkins'
//	}
//	}
//    }
	  
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
