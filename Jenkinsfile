node {

    stage('Checkout') {
       checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/seigenbrode/byo-scenario']]])
    }

    stage('BuildContainer') {

 	   def customImage = docker.build("scikit-byo:${env.BUILD_ID}")

    }

    stage('PublishContainerImage') {
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