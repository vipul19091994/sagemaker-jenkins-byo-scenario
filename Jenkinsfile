pipeline {

    agent any

    stages {
        stage("Checkout") {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/seigenbrode/byo-scenario']]])
            }
        }

        stage("BuildContainer") {
            steps {
              sh """
 	              docker build -t scikit-byo:${env.BUILD_ID}
              """
            }
        }

        stage("PublishContainerImage") {
     	      steps { 
              sh """
             	echo "${env.ECRURI}""
           		sh("eval \$(aws ecr get-login-password --region us-east-1 | sed 's|https://||')")
           		sh 'docker push "${params.ECRURI}":customImage'
          }
        }
    }
}   