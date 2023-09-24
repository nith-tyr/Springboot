pipeline {
  agent{
  tools {
    jdk 'Java17'
    maven 'Maven3'
  }
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
    stage ("Build Application") { 
      steps {
        sh "mvn clean package"
      }
    }
    stage ("Test Application") { 
      steps {
        sh "mvn test"
      }
    }
    
  }
}

