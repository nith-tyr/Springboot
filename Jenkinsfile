pipeline {
  agent { 
	docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
}
     stages {
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
	  
   stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "nitheeshbp/ultimate-cicd:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-login')
      }
      steps {
        script {
            sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-login") {
                dockerImage.push()
            }
        }
      }
    }

       stages {
    stage('Build') {
      steps {
        echo 'Building...'
      }
    }
    stage('Test') {
      steps {
        echo 'Testing...'
        snykSecurity(
          snykInstallation: 'Snyk',
          snykTokenId: '134e4d35-e9be-43db-a9b3-29c13e281698',
          // place other parameters here
        )
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploying...'
      }
    }
  }
}
   
     }	  
}
