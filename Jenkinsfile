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
        
        stage("TrainModel") {
            steps { 
              sh """
               aws sagemaker create-training-job --training-job-name ${params.SAGEMAKER_TRAINING_JOB}-${env.BUILD_ID} --algorithm-specification TrainingImage="${params.ECRURI}:${env.BUILD_ID}",TrainingInputMode="File" --role-arn ${params.SAGEMAKER_EXECUTION_ROLE_TEST} --resource-config InstanceType='ml.c4.2xlarge',InstanceCount=1,VolumeSizeInGB=5 --output-data-config S3OutputPath='${S3_MODEL_ARTIFACTS}' --stopping-condition MaxRuntimeInSeconds=3600
              """
             }
        }

      stage("TrainMonitor") {
          steps {
              script {
                  waitUntil {
                    def response = sh """ 
                    aws lambda invoke --function-name ${params.LAMBDA_CHECK_STATUS_TRAINING} --cli-binary-format raw-in-base64-out --region us-east-1 --payload '{"TrainingJobName": "${params.SAGEMAKER_TRAINING_JOB}"-"${env.BUILD_ID}"}' response.json
                    status=$(cat ./response.json)
                    echo status
                    """
                    status == "Completed" || status == "Failed"
                  }
              }
        }
    }
  }
}   
