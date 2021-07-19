pipeline {

    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_DEFAULT_REGION = 'us-east-1'
        AWS_ECR_LOGIN = 'true'
        DOCKER_CONFIG= "${params.JENKINSHOME}"
    }

    stages {
        stage("Checkout") {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/vipul19091994/sagemaker-jenkins-byo-scenario']]])
            }
        }

        stage("BuildPushContainer") {
            steps {
              sh """
                echo "${params.ECRURI}"
                aws --version
                """
            }
        }
        
        
 }
}   
