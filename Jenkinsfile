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
       
          "Check files": {
            node {
                    withCredentials([[$class: 'StringBinding', credentialsId: 'gitvalentinab2', variable: 'GITHUB_TOKEN']]) {
                    curl -s -H "Authorization: bearer $BEARER_TOKEN" 
                    def apiUrl = "https://api.github.com/repos/${env.GIT_ORG}/${env.GIT_NAME}/pulls/${env.CHANGE_ID}/files"
                    def response = sh(returnStdout: true, script: "curl -s -H \"Authorization: Token ${env.GITHUB_TOKEN}\" ${apiUrl}").trim()
                    def jsonSlurper = new JsonSlurper()
                    def files_changed = jsonSlurper.parseText(response)
                    def check_version_changed = false
                    def check_history_changed = false
                    files_changed.each {
                          if ($it.filename == "eea/progressbar/version.txt" and $it.status == "modified")  {
                                check_version_changed = true
                          }
                          if ($it.filename == "docs/HISTORY.txt" and $it.status == "modified")  {
                                check_history_changed = true
                          }
                     }
                      if (! check_history_changed) {                       
                          error "Pipeline aborted due to no history file changed"
                      }
                      if (! check_history_changed) {                       
                          error "Pipeline aborted due to no version changed"
                      }
                      
      
                      }                 
                  }
             
            }
          }
        
      }
}
  }  
  
}
