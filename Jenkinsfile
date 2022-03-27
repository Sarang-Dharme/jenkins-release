/* Pipeline */

pipeline {
  agent any

/* Dynamic variable assignment */

  parameters {
      choice (
          name: 'BuildType',
          choices:"SNAPSHOT\nRELEASE",
          description: "Choose the Build type"
      )
  }


  options { 
    // ansiColor('xterm') 
    disableConcurrentBuilds()
  }

  tools {
    jdk 'java8'
    maven 'maven-3.6.0'
    }

  environment {
    NEXUS_FQDN     = 'ec2-15-206-189-141.ap-south-1.compute.amazonaws.com'
    NEXUS_PORT     = '8081'
    ARTIFACT_VERSION = '1.0.0-SNAPSHOT'
    JAVA_HOME      = "/usr/java/jdk1.8.0_191-amd64"
    MAVEN_HOME     = "/opt/maven"
    NEXUS_CREDS = credentials('nexus_credentials')
  }

  stages {
    stage('clean & verify') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'nexus_credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
          withMaven(globalMavenSettingsFilePath: 'settings.xml', jdk: "java8", maven: "maven-3.6.0") {
            sh "mvn -Drevision=${ARTIFACT_VERSION} -Dserver.username=${USERNAME} -Dserver.password=${PASSWORD} clean verify"
          }
        }
      }
    }
  }

  stage('deploy') {
      when {
          expression { params.BuildType == 'SNAPSHOT' }
      }
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'nexus_credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
          withMaven(globalMavenSettingsFilePath: 'settings.xml', jdk: "java8", maven: "maven-3.6.0") {
            sh "mvn -Drevision=${ARTIFACT_VERSION} -Dserver.username=${USERNAME} -Dserver.password=${PASSWORD} deploy"
          }
        }
      }
    }
  }

  stage('release') {
      when {
          expression { params.BuildType == 'RELEASE' }
      }
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'nexus_credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
          withMaven(globalMavenSettingsFilePath: 'settings.xml', jdk: "java8", maven: "maven-3.6.0") {
            sh "mvn -Drevision=${ARTIFACT_VERSION} -Dserver.username=${USERNAME} -Dserver.password=${PASSWORD} release:clean release:prepare release:perform"
          }
        }
      }
    }
  }
}
}
