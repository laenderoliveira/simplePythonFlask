pipeline {
	agent any
	
	options {
	  timestamps()
	  timeout(time: 10, unit: "MINUTES")
	}	
	
	environment {
	  IMAGE_NAME="simple-flask-app"
	  IMAGE_TAG="0.${BUILD_ID}"
	  CONTAINER_NAME="${IMAGE_NAME}:${IMAGE_TAG}"
	  HTTP_PROTOCOL="http://"
	  NEXUS_REPOSITORY="192.168.88.20:8082"
      DOCKER_REGISTRY="${HTTP_PROTOCOL}${NEXUS_REPOSITORY}"
	}
	stages{
	  stage("build"){
	    steps {
	      script {
	        image = docker.build("${CONTAINER_NAME}")
		image.inside("-v ${WORKSPACE}:/simplePythonApplication"){
		  sh "nosetests --with-xunit --cover-package=project test_users.py"
		}
	      }
	    }
	  }

	  // stage('Code Analysis'){ 
//		   steps{ 
//			   script{ 
//				   def sonarScannerPath = tool 'SonarScanner' withSonarQubeEnv('SonarQube'){ 		
//					   sh "${sonarScannerPath}/bin/sonar-scanner -Dsonar.projectKey=simple-flask-app -Dsonar.sources=." 
//					   } 
//					} 
//				} 
//			}
//		stage('Code Analysis Result'){
//			steps {
//				timeout(time: 1, unit:'MINUTES') {
//					waitForQualityGate abortPipeline: true
//				}
//			}
//		}

		stage("Nexus - Saving Artifact"){
 			steps{
 				script{
					 docker.withRegistry("${DOCKER_REGISTRY}", "8f6051d4-fd33-445e-9973-b96cc9118fef"){
 				      image.push()
 					}
 				}
 			}
 		}
	}
	post {

		success {
			echo "Pipeline finalizado com sucesso!!!!"
		}

		failure {
			echo " Pipeline erro"
		}

		cleanup {
			//sh "docker image rm ${CONTAINER_NAME}"
		}

		always {
			junit 'nosetests.xml'
		}
	}
}