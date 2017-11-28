#!groovy
import groovy.json.JsonOutput
import groovy.json.JsonSlurper

pipeline {
  agent any

  environment {
        GIT_NAME = "eea.progressbar"
        GIT_ORG = "valentinab25"
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
                      script{
                        def apiUrl = "https://api.github.com/repos/${env.GIT_ORG}/${env.GIT_NAME}/pulls/${env.CHANGE_ID}/files"
                        def response = sh(returnStdout: true, script: "curl -s -H \"Authorization: Token ${env.GITHUB_TOKEN}\" ${apiUrl}").trim()
                        def jsonSlurper = new JsonSlurper()
                        def files_changed = jsonSlurper.parseText(response)
                        def check_version_changed = "false"
                        def check_history_changed = "false"
                        files_changed.each {
                                 
                              if (it["filename"] == "eea/progressbar/version.txt" && it["status"] == "modified")  {
                                    check_version_changed = "true"
                              }
                              if (it["filename"] == "docs/HISTORY.txt" && it["status"] == "modified")  {
                                    check_history_changed = "true"
                              }
                         }
                        
                        if (check_history_changed == "false") {                       
		             error "Pipeline aborted due to no history file changed"
		         }
		         if (check_version_changed == "false" ) {                       
		             error "Pipeline aborted due to no version changed"
                          }
                              checkout scm 
			      version = readFile 'eea/progressbar/version.txt'
			      echo "${version}"
			      
                        
                      }
                      }         
           
            }
          },
		   "Check versions": {
            node(label: 'docker-1.13') {
                    withCredentials([string(credentialsId: 'GitHubTokentest', variable: 'GITHUB_TOKEN')]) {
                      script{
                                   checkout scm 
			      version = readFile 'eea/progressbar/version.txt'
			      echo "${version}"
			      
                        
                      }
                      }         
           
            }
          }
          )
      }
}
  }  
  
}

