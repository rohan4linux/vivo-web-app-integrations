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
    }
  }           
   
