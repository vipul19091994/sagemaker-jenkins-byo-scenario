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
 	              docker build -t scikit-byo:${env.BUILD_ID} .
              """
            }
        }

        stage("PublishContainerImage") {
     	      steps { 
              sh """
                echo "${params.ECRURI}"
                docker tag scikit-byo:${env.BUILD_ID} ${params.ECRURI}:${env.BUILD_ID}
                docker push scikit-byo:${env.BUILD_ID}
           		"""
          }
        }
    }
}   