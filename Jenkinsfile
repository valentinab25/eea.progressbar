#!groovy

import groovy.json.JsonSlurper

pipeline {
  agent any

  environment {
        GIT_NAME = "eea.progressbar"
        GIT_ORG = "valentinab25"
        GIT_VERSIONFILE = "eea/progressbar/version.txt"
        GIT_HISTORYFILE = "docs/HISTORY.txt"
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
			sh '''env'''
                        def apiUrl = "https://api.github.com/repos/${env.GIT_ORG}/${env.GIT_NAME}/pulls/${env.CHANGE_ID}/files"
                        def response = sh(returnStdout: true, script: "curl -s -H \"Authorization: Token ${env.GITHUB_TOKEN}\" ${apiUrl}").trim()
                        def jsonSlurper = new JsonSlurper()
                        def files_changed = jsonSlurper.parseText(response)
                        def check_version_changed = "false"
                        def check_history_changed = "false"
                        files_changed.each {
                                 
				if (it["filename"] == "${GIT_VERSIONFILE}" && it["status"] == "modified")  {
                                    check_version_changed = "true"
                              }
				if (it["filename"] == "${GIT_HISTORYFILE}" && it["status"] == "modified")  {
                                    check_history_changed = "true"
                              }
                         }
                        
                        if (check_history_changed == "false") {                       
		             error "Pipeline aborted due to no history file changed"
		         }
		         if (check_version_changed == "false" ) {                       
		             error "Pipeline aborted due to no version changed"
                          }
                                                   
                      }
                      }         
           
            }
          },
		   "Check versions": {
            node(label: 'docker-1.13') {
                    withCredentials([string(credentialsId: 'GitHubTokentest', variable: 'GITHUB_TOKEN')]) {
                      script{
                                   checkout scm 
			      def version_temp = readFile GIT_VERSIONFILE
			      def version = version_temp.trim()
			      
			      def apiUrl = "https://api.github.com/repos/${env.GIT_ORG}/${env.GIT_NAME}/tags"
				def response = sh(returnStdout: true, script: "curl -s -H \"Authorization: Token ${env.GITHUB_TOKEN}\" ${apiUrl}").trim()
				def jsonSlurper = new JsonSlurper()
				def tags = jsonSlurper.parseText(response)
				def check_version_is_new = "true"
				def last_version = tags[0]["name"]

			      if ( ! ( version ==~ /(\d+)\.(\d+)/ ) ) {
			        error "Version ${version} does not respect format: \"number.number\", please change it"
			      }
				      
				      
			      tags.each {                      
	                 	      if ( it['name'].equalsIgnoreCase( version ) )  {
                                         check_version_is_new = "false"
                                        }
                              }
                        
				if (check_version_is_new == "false") {                       
				     error "Pipeline aborted due to version already present in tags"
				 }
			      
			        List verA = version.tokenize('.')
				List verB = last_version.tokenize('.')
   			        for (int i = 0; i < 2; ++i) {
				      def numA = verA[i].toInteger()
				      def numB = verB[i].toInteger()
				      if (numA != numB) {
					result = numA <=> numB
					break     
				      }
				    }


			    if ( result == -1 )	{                       
				     error "Pipeline aborted due to version ${version} being smaller than last version ${last_version}"
				 }				    
                        
                      }
                      }         
           
            }
          }
          )
      }
}
	  
  stage('Master branch release and tag') {
      when {
                allOf {
                    environment name: 'CHANGE_ID', value: ''
                    branch 'master'
                }
            }
     steps {
 parallel(
          "Release": {
            node(label: 'docker-1.13') {
		 checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'eea.plonebuildout.core']], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/eea/eea.plonebuildout.core.git']]])
   
		    
		    
            }
          },
		
          "Tag": {
            node(label: 'docker-1.13') {       
            }
          }	
          )
      }
}
	  
  }  
  
}
