#!groovy
import groovy.json.JsonOutput
import groovy.json.JsonSlurper

pipeline {
  agent any

  environment {
        GIT_NAME = "eea.progressbar"
        GIT_ORG = "eea"
    }

  stages {
   
  stage('Pull request checks') {
           when {
                not {
                    environment name: 'CHANGE_ID', value: ''
                }
            }
     steps {
        parallel(

          "Check files": {
            node(label: 'docker-1.13') {
                    withCredentials([string(credentialsId: 'GitHubTokentest', variable: 'GITHUB_TOKEN')]) {
                    
      
                      }         
           
            }
          }
          )
      }
}
  }  
  
}

