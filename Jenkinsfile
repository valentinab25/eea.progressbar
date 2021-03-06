pipeline {
  agent any

  environment {
        GIT_NAME = "eea.progressbar"
    }

  stages {
        
stage('Functional tests') {
      steps {
        parallel(

          "WWW": {
            node(label: 'docker-1.13') {
                script {
                try {
                  checkout scm
                  sh '''docker run -p 8080 -d --name=$BUILD_TAG-ft-www  -e GIT_BRANCH="$BRANCH_NAME" -e GIT_CHANGE_ID="$CHANGE_ID" -e ADDONS="$GIT_NAME" -e DEVELOP="src/$GIT_NAME" eeacms/www-devel'''
                  sh '''docker port $BUILD_TAG-ft-www 8080/tcp > url.file;docker_ip=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}' $BUILD_TAG-ft-www); sed -i -e "s/0.0.0.0/${docker_ip}/g" url.file'''
                  sh '''new_url=$(cat url.file);timeout 600  wget --retry-connrefused --tries=60 --waitretry=10 -q http://${new_url}/'''
                  sh '''new_url=$(cat url.file);casperjs test eea/progressbar/ftests/eea/*.js --url=${new_url} --xunit=ftestsreport.xml'''
                }
                finally {
                  sh '''docker stop $BUILD_TAG-ft-www'''
                  sh '''docker rm -v $BUILD_TAG-ft-www'''
                }
               }
              junit 'ftestsreport.xml'
              archiveArtifacts 'screenshot_eea.png'

              }
                   },

          "KGS": {
            node(label: 'docker-1.13') {
                  script {
                try {
                  checkout scm
                  sh '''docker run -p 8080 -d --name=$BUILD_TAG-ft-kgs  -e GIT_BRANCH="$BRANCH_NAME" -e GIT_CHANGE_ID="$CHANGE_ID" -e ADDONS="$GIT_NAME" -e DEVELOP="src/$GIT_NAME" eeacms/kgs-devel'''
                  sh '''docker port $BUILD_TAG-ft-kgs 8080/tcp > url.file;docker_ip=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}' $BUILD_TAG-ft-kgs); sed -i -e "s/0.0.0.0/${docker_ip}/g" url.file'''
                  sh '''new_url=$(cat url.file);timeout 600  wget --retry-connrefused --tries=60 --waitretry=10 -q http://${new_url}/'''
                  sh '''new_url=$(cat url.file);casperjs test eea/progressbar/ftests/kgs/*.js --url=${new_url} --xunit=ftestsreport.xml'''
                }
                finally {
                  sh '''docker stop $BUILD_TAG-ft-kgs'''
                  sh '''docker rm -v $BUILD_TAG-ft-kgs'''
                }
               }
              junit 'ftestsreport.xml'
               archiveArtifacts 'screenshot_kgs.png'
            }
          },

          "Plone4": {
            node(label: 'docker-1.13') {
              script {
                try {
                  checkout scm
                  sh '''docker run -p 8080 -d --name=$BUILD_TAG-ft-plone4  -e GIT_BRANCH="$BRANCH_NAME" -e GIT_CHANGE_ID="$CHANGE_ID" -e ADDONS="$GIT_NAME" -e DEVELOP="src/$GIT_NAME" eeacms/plone-test:4'''
                  sh '''docker port $BUILD_TAG-ft-plone4 8080/tcp > url.file;docker_ip=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}' $BUILD_TAG-ft-plone4); sed -i -e "s/0.0.0.0/${docker_ip}/g" url.file'''
                  sh '''new_url=$(cat url.file);timeout 600  wget --retry-connrefused --tries=60 --waitretry=10 -q http://${new_url}/'''
                  sh '''new_url=$(cat url.file);casperjs test eea/progressbar/ftests/plone4/*.js --url=${new_url} --xunit=ftestsreport.xml'''
                }
                finally {
                  sh '''docker stop $BUILD_TAG-ft-plone4'''
                  sh '''docker rm -v $BUILD_TAG-ft-plone4'''
                }
               }
              junit 'ftestsreport.xml'
              archiveArtifacts 'screenshot_plone4.png'

              }

            }
          )
      }
}
  }  
  
}
