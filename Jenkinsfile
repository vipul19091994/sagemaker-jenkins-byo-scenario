pipeline {

    agent any

    environment {
        AWS_ECR_LOGIN = 'true'
        DOCKER_CONFIG= "${params.JENKINSHOME}"
    }

    stages {
        stage("Checkout") {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/seigenbrode/byo-scenario']]])
            }
        }

        stage("BuildPushContainer") {
            steps {
              sh """
                echo "${params.ECRURI}"
                aws ecr get-login-password --region us-east-1 | docker login --username AWS   --password-stdin ${params.ECRURI}
 	              docker build -t scikit-byo:${env.BUILD_ID} .
                docker tag scikit-byo:${env.BUILD_ID} ${params.ECRURI}:${env.BUILD_ID} 
                docker push ${params.ECRURI}:${env.BUILD_ID}
                echo ${params.S3_PACKAGED_LAMBDA}
              """
            }
        }
        
        stage("DeployTrainingLambda") {
            steps {
              sh """ 
              aws lambda create-function --function-name MLOps-Jenkins-TrainModel-ScikitBYO --runtime python3.6 --role ${params.LAMBDA_EXECUTION_ROLE_TEST} --handler MLOps-TrainModel.lambda_handler --code S3Bucket=${params.S3_PACKAGED_LAMBDA},S3Key='MLOps-Jenkins-TrainModel-ScikitBYO.zip'             
              """
          }
        }

        stage("TrainModel") {
            steps { 
              sh """
               echo "Start Training"
              """
             }
        }
    }
}   
