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
              """
            }
        }

        stage("TrainModel") {
            steps { 
              sh "cd train_cfg"
              samDeploy([credentialsId: '${params.AWS_DEPLOYUSER}', kmsKeyId: '${params.KMSKEYID}', outputTemplateFile: '', parameters: [[key: 'S3PackagedLambdaCode', value: '${params.S3_PACKAGED_LAMBDA}'],[key: 'SageMakerExecutionRole', value: '${SAGEMAKER_EXECUTION_ROLE_TEST}']], region: 'us-east-1', roleArn: '${LAMBDA_EXECUTION_ROLE_TEST}', s3Bucket: '${S3_PACKAGED_LAMBDA}', s3Prefix: '${env.BUILD_ID}', stackName: 'cfn-deploy-trainmodel-lambda', templateFile: './train_cfg/sam-template-trainmodel.yml'])
          }
        }
    }
}   