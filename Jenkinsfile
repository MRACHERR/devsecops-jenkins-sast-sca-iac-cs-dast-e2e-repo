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
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          bat("mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=squ_dd18ace11e709f2270cfbb884193a7dbf4ee6b9a -Dsonar.projectKey=easybuggy -Dsonar.host.url=http://localhost:9000/")
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
        bat("C:\\Users\\ANASS\\OneDrive\\Documents\\ZAP_2.12.0\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout C:\\Users\\ANASS\\OneDrive\\Documents\\ZAP_2.12.0\\Output.html")
      }
    }
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              bat("C:\\snyk\\snyk-win.exe  container test anasschhh/testeb")
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }

    stage('Build') {
      steps {
        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("anasschhh/testeb")
          }
        }
      }
    }
    
    
    stage('checkov') {
      steps {
        bat("checkov -s -f main.tf")
      }
    }

  }
}
