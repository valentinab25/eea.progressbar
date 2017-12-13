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
	 
 parallel(
          "Check PR": {
            node(label: 'docker-1.13') {
                    	    
		    if ( ${CHANGE_BRANCH} != "develop" &&  ${CHANGE_BRANCH} != "hotfix" ) {                       
		             error "Pipeline aborted due to PR not made from develop or hotfix branch"
		         }      
                    
		try {
                  sh '''docker run -i --name="$BUILD_TAG-gitflow-pr" -e GIT_BRANCH="$BRANCH_NAME" -e GIT_CHANGE_ID="$CHANGE_ID" -e GIT_SRC="$GIT_URL" -e GIT_VERSIONFILE="$GIT_VERSIONFILE" -e GIT_HISTORYFILE="$GIT_HISTORYFILE" -e GIT_NAME="$GIT_NAME" gitflow'''
                } finally {
                  sh '''docker rm -v $BUILD_TAG-gitflow-pr'''
                   }
		  
		    
            }
          },
	  "Tag": {
            node(label: 'docker-1.13') {       
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
