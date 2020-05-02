def IMAGE_NAME="byo-scikit"
def IMAGE_TAG=${env.BUILD_ID}

node {

    stage { 
    
      stage('Checkout') {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/seigenbrode/byo-scenario']]])
       }

       stage('BuildContainer') {
           imageBuild(IMAGE_NAME, IMAGE_TAG)
       }

   	   stage ('PublishContainerImage') {
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
	}    

def imageBuild(IMAGE_NAME, IMAGE_TAG){
    sh "docker build -t $containerName:$tag  -t $containerName --pull --no-cache ."
    echo "Image build complete"
}
