def CONTAINER_NAME="jenkins-pipeline"
def CONTAINER_TAG= "latest"
def DOCKER_HUB_USER= "dhiraj2029"
def HTTP_PORT=  "8090"

node{
		stage('initialize')
		{
		   def dockerHome= tool 'myDocker'
		   def mavenHome= tool 'myMaven'
		   env.PATH= "${dockerHome}/bin:${mavenHome}/bin:${env.PATH}"
		}
		
		stage('checkout')
		{
		    checkout scm
		}
		
		stage('Build')
		{
		    sh "mvn clean install"
		}
		
		stage('sonar')
		{
			try
			{
				sh "mvn sonar:sonar"
			}
			catch(error)
			{
				echo "The sonar service could not reached ${error}"
			}
		}
		
		stage ('Image Prune')
		{
			imagePrune(CONTAINER_NAME)
		}
		
		
		

		
	
	}
	
	
	    def imagePrune(containerName)
		{
			try
			{
				sh "docker image prune -f"
				sh "docker stop $containerName"
			}
			
			catch(error)
			{
			}
			
		}
