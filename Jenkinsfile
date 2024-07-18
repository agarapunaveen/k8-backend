pipeline {
    agent {
        label 'agent-1'
    }
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
   
    environment{
       def appVersion = ''
       def nexusUrl= 'nexus.naveencloud.online:8081'
       def region='us-east-1'
       def account_id='211125718262'
    }
    stages {
        stage('read the version'){
            steps{

              script { 
                def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                
                }
            }
        }
        stage('Install dependencies') {
            steps {
                sh """
                 npm install
                 ls -ltr
                """
            }
        }
        stage('Build'){
            steps{
               sh """
                    zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                    ls -ltr
                """
            }
        }

        stage ('Docker Build'){
            steps{
                sh """
                  aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${account_id}.dkr.ecr.${region}.amazonaws.com

                  docker build -t expense-backend .

                  docker tag expense-backend:latest ${account_id}.dkr.ecr.us-east-1.amazonaws.com/expense-backend:${appVersion}

                  docker push ${account_id}.dkr.ecr.us-east-1.amazonaws.com/expense-backend:${appVersion}
                """
            }
        }

        // stage('sonar scanar'){
        //     environment {
        //         scannerHome = tool 'sonar'
        //     }
        //     steps {
        //         script {
        //             withSonarQubeEnv('sonar') {
        //                 sh "${scannerHome}/bin/sonar-scanner"
                           
        //             }
        //         }
        //     }
        // }

        // stage('nexus artifact upload'){
        //     steps{
        //       script{
        //         nexusArtifactUploader(
        //         nexusVersion: 'nexus3',
        //         protocol: 'http',
        //         nexusUrl: "${nexusUrl}",
        //         groupId: 'com.example',
        //         version: "${appVersion}",
        //         repository: "backend",
        //         credentialsId: 'nexus',
        //         artifacts: [
        //             [artifactId: "backend",
        //             classifier: '',
        //             file: 'backend-' + "${appVersion}" + '.zip',
        //             type: 'zip']
        //         ]
        //     )
        //       }
        //     }
        // }
        // stage("deploy"){
        //     steps{
               
        //         script{
        //              def params =[
        //                string(name: 'appVersion',value: "${appVersion}")
        //             ]
        //              build job: 'backend-deploy', parameters: params,wait: false
        //         }
        //     }
        // }
    }
        post {
            always{
                echo ' i will always say hello again'
                deleteDir()
            }
            success{
                echo ' i will run the pipeline success'
            }
            failure{
                echo ' i will run the pipeline failure'
            }
        }
}