pipeline {
     agent any
     
        environment {
            def BUILDVERSION = sh(script: "echo `date +%Y%m%d`", returnStdout: true).trim()
            def BUILDFULLNAME = "${BUILDVERSION}_1.0.${BUILD_NUMBER}"
            
            //once you create ACR in Azure cloud, use that here
            registryName = "acrlab1"
            //- update your credentials ID after creating credentials for connecting to ACR
            registryCredential = 'ACR_Mati'
            dockerImage = ''
            registryUrl = 'acrlab1.azurecr.io'
    }
    
    stages {

        stage ('checkout') {
            steps {
            checkout scmGit(branches: [[name: '*/dockercomposik']], extensions: [], userRemoteConfigs: [[credentialsId: 'gitpls', url: 'https://github.com/mateuszrog/Jenkins']])
            }
        }
       
        stage ('Build Docker image') {
            steps {
                
                script {
                    sh 'echo "Current build version :: ${BUILDFULLNAME}"'
                    sh 'echo "BUILDFULLNAME=${BUILDFULLNAME}\nregistryUrl=${registryUrl}" > .env'
                    sh 'echo "${BUILDFULLNAME}" >> Versioning.txt'
                    sh 'docker context use default'
                    sh 'docker context ls'
    
                    sh 'docker-compose build --pull'
                    sh 'docker logout'
                }
            }
        }
       
    // Uploading Docker images into ACR
    stage('Upload Image to ACR') {
     steps{   
         script {
            
            docker.withRegistry("http://${registryUrl}", registryCredential) {
                sh  'docker-compose push'
                sh 'docker context ls'
                sh 'docker context create aci mateuszaci --resource-group ACR'
                sh 'docker context ls'
                sh 'docker context use mateuszaci'
                // sh 'docker --context mateuszaci volume create test-volume --storage-account mateuszrogstorage'
                sh 'docker --context mateuszaci volume ls'
                sh 'docker compose up -d'

            }
        }
      }
    }

       // Stopping Docker containers for cleaner Docker run
     stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
         }
      }
      
    // stage('Docker Run') {
    //  steps{
    //      script {
    //             sh 'docker run -d -p 8096:5000 --rm --name mypythonContainer ${registryUrl}/${registryName}'
    //         }
    //   }
    // }
    }
 }
