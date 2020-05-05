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
              samDeploy([credentialsId: '${params.AWS_DEPLOYUSER}', kmsKeyId: '${params.KMSKEYID}', parameters: [[key: 'S3PackagedLambdaCode', value: '${params.S3_PACKAGED_LAMBDA}'],[key: 'SageMakerExecutionRole', value: '${params.SAGEMAKER_EXECUTION_ROLE_TEST}']], region: 'us-east-1', roleArn: '${params.LAMBDA_EXECUTION_ROLE_TEST}', s3Bucket: '${params.S3_PACKAGED_LAMBDA}', stackName: 'cfn-deploy-trainmodel-lambda', templateFile: './train_cfg/sam-template-trainmodel.yml'])
          }
        }
    }
}   