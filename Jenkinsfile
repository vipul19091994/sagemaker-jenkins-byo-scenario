node {

    stage('Checkout') {
       checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/seigenbrode/byo-scenario']]])
    }

    stage('BuildContainer') {

 	   def customImage = docker.build("scikit-byo:${env.BUILD_ID}")

    }

    stage('PublishContainerImage') {
     	script { 
           		 sh 'echo "ECR URI"'
           		 sh 'echo ${ECRURI}'
           		 sh("eval \$(aws ecr get-login-password --region us-east-1 | sed 's|https://||')")
           		 sh 'docker  push  ${params.ECRURI}:customImage'
        }
    }    