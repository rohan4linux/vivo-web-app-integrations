pipeline {
  agent any
  tools {
  
  maven 'maven'
   
  }
    stages {

      stage ('Checkout SCM'){
        steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/rohan4linux/mobile-web-app-integrations.git']]])
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
                 sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=iwayq-web-test'
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
                    releaseRepo: "iwayqweb-libs-release-local",
                    snapshotRepo: "iwayqweb-libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "jfrog",
                    releaseRepo: "iwayqweb-libs-release",
                    snapshotRepo: "iwayqweb-libs-snapshot"
                )
            }
    }

    stage ('Deploy Artifacts') {
            steps {
                rtMavenRun (
                    tool: "maven", // Tool name from Jenkins configuration
                    pom: 'java-source/pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
         }
    }

    stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "jfrog"
             )
        }
    }
    stage('Build Container Image') {
            
            steps {
              sh "ansible-playbook create-container-image.yaml"       
            }
            
        } 

    stage('Build Helm Charts') {
            
            steps {
              dir('charts') {
             sh "/bin/helm package mobile-web-app"
					   sh "sudo /bin/helm push-artifactory --username rohan.reddy529@gmail.com --password 4getmenot@J mobile-web-app-0.0.1.tgz https://valaxytech529.jfrog.io/artifactory/iwayqweb-libs-release-local"
					  }
          }
            
        } 
         
   } 
}