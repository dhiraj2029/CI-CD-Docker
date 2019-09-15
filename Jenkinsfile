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
		
		stage('Image Prune')
		{
			imagePrune(CONTAINER_NAME)
		}
		
		stage('Image Build')
		{
			imageBuild(CONTAINER_NAME,CONTAINER_TAG)
		}
		
		stage('Push to Docker Registry')
		{
			withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])
			{
				pushToImage(CONTAINER_NAME, CONTAINER_TAG, USERNAME, PASSWORD)
			}
		}
		
		stage('Run App')
		{
			runApp(CONTAINER_NAME, CONTAINER_TAG, DOCKER_HUB_USER, HTTP_PORT)
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
		
		def imageBuild(containerName,tag)
		{
		
			try
			{
				sh "docker build -t $containerName:$tag -t $containerName --pull --no-cache ."
				echo "Image build successfully"
			}
			catch(error)
			{
			}
		}
		
		def pushToImage(containerName, tag, dockerUser, dockerPassword)
		{
			sh "docker login -u $dockerUser -p $dockerPassword"
			sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
			sh "docker push $dockerUser/$containerName:$tag"
			echo "Image push complete"
		}
		
		def runApp(containerName, tag, dockerHubUser, httpPort)
		{
			sh "docker pull $dockerHubUser/$containerName"
			sh "docker run -d --rm -p $httpPort:$httpPort --name $containerName $dockerHubUser/$containerName:$tag"
			echo "Application started on port: ${httpPort} (http)"
		}
