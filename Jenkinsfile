#!groovy

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
		 environment name: 'CHANGE_TARGET', value: 'master'
            }
     steps {
          "Check PR": {
            node(label: 'docker-1.13') {
                    	    
		
		  
		    
            }
          }
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
