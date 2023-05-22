pipeline {
     agent any
     
        environment {
            def BUILDVERSION = sh(script: "echo `date +%Y%m%d`", returnStdout: true).trim()
            def BUILDFULLNAME = "${BUILDVERSION}_1.0.${BUILD_NUMBER}"
            
            //once you create ACR in Azure cloud, use that here
            registryName = "acrdevopslab"
            //- update your credentials ID after creating credentials for connecting to ACR
            registryCredential = 'ACR'
            dockerImage = ''
            registryUrl = 'acrdevopslab.azurecr.io'
    }
    
    stages {

        stage ('checkout') {
            steps {
            checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/michalstudenny/DockerAcrAci']])
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
                    sh '/usr/local/bin/docker login azure'
    
                    sh 'docker-compose build --pull'
                    sh 'docker logout'
                }
            }
        }
       
    // Uploading Docker images into ACR //
    stage('Upload Image to ACR') {
     steps{   
         script {
            
            docker.withRegistry("http://${registryUrl}", registryCredential) {
                sh  'docker-compose push'
                sh 'docker context ls'
                sh 'docker context create aci michalaci --resource-group ACR'
                sh 'docker context ls'
                sh 'docker context use michalaci'
                //sh 'docker --context michalaci volume create test-volume --storage-account michaldevopsstorage'
                sh 'docker --context michalaci volume ls'
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
