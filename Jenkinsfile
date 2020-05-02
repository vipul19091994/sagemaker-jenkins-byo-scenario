pipeline {

    agent any

    stages { 
    
        stage('Checkout') {
           checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/seigenbrode/byo-scenario']]])
    	}

    	stage('BuildContainer') {

 	      def customImage = docker.build("scikit-byo:${env.BUILD_ID}")

    	}

   		stage('PublishContainerImage') {
       		 steps {
      		      script
           			     {
               			     sh("eval \$(aws ecr get-login --no-include-email | sed 's|https://||')")
                   			 // Push the Docker image to ECR
                  			 docker.withRegistry(${params.ECRURI})
                   			    {
                        		docker.image(customImage).push()
                    			}
                  }
            }
        }
    
    	post
    	{
       	 always
       	 {
            // make sure that the Docker image is removed
            sh "docker rmi $IMAGE | true"
         }
        }
	}    
}
