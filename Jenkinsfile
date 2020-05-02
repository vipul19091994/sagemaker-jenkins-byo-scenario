node {

    stage('Checkout') {
       checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/seigenbrode/byo-scenario']]])
    }

    stage('BuildContainer') {

 	   def customImage = docker.build("scikit-byo:${env.BUILD_ID}")

    }

    stage('PublishContainerImage') {

    }
}
