pipeline {
  agent any
  tools {
  
  maven 'maven'
   
  }
    stages {

      stage ('Checkout SCM'){
        steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/rohan4linux/vivo-web-app-integrations.git']]])
        }
      }
    
  
            
      stage ('Build')  {
	      steps {
          dir('java-source'){
          sh "mvn package"
          }
        }     
      }   
     

      stage ('SonarQube Analysis') {
        steps {
          withSonarQubeEnv('sonar') {
          dir('java-source'){
          sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=antrix'

                }	
              }
            }
          }
          

      stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "jfrog",
                    url: "https://valaxytech529.jfrog.io/artifactory",
                    credentialsId: "jfrog"
                )
           
                 rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "jfrog",
                    releaseRepo: "vivo-libs-release-local",
                    snapshotRepo: "vivo-libs-snapshot-local"
                )
               

                 rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "jfrog",
                    releaseRepo: "vivo-libs-release",
                    snapshotRepo: "vivo-libs-snapshot"
                ) 
             }
          }
        }
      }               
