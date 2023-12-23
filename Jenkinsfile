pipeline {
  agent any
  environment {
        // Set the JAVA_HOME variable to the path of your JDK installation
        JAVA_HOME = "C:\\Program Files\\Java\\jdk-11"
        // Optionally, update the PATH variable to include the JDK bin directory
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
        
    }
  tools {
    maven 'Maven_3_8_7'
  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
  	docker.image("anasschhh/testeb").inside {
    	// Run a command inside the Docker container
    	sh 'echo "Credentials are valid!"'
  }
}
        }
      }
    
    stage('Build') {
       steps {
    script {
      // Hardcoded Docker registry credentials
      def registryCredentials = [
        credentialsId: 'dockerlogin',
        url: 'https://registry.example.com' // Replace with your Docker registry URL
      ]

      // Use the hardcoded credentials for Docker registry
      withDockerRegistry(registryCredentials) {
        app = docker.build("anasschhh/testeb")
      }
    }
  }
    }
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              bat("C:\\snyk\\snyk-win.exe  container test asecurityguru/testeb")
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunSnykSCA') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          bat("mvn snyk:test -fn")
        }
      }
    }
    stage('RunDASTUsingZAP') {
      steps {
        bat("C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\Output.html")
      }
    }

    stage('checkov') {
      steps {
        bat("checkov -s -f main.tf")
      }
    }

  }
}
