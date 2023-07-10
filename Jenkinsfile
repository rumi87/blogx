pipeline {
  agent any 
  environment {
    GIT_COMMIT_SHORT = sh(returnStdout: true, script: '''echo $GIT_COMMIT | head -c 7''')
  }
  stages {
    stage('Prepare .env-commit') {
      steps {
        sh 'echo GIT_COMMIT_SHORT=$(echo $GIT_COMMIT_SHORT) > .env-commit'
      }
    }
    stage('Build') { 
      steps {
        sh 'docker build . -t rumi87/blogx:$GIT_COMMIT_SHORT'
        sh 'docker push rumi87/blogx:$GIT_COMMIT_SHORT'
      }
    }
    stage('Deploy to remote server') {
      steps {
        sshPublisher(publishers: [sshPublisherDesc(configName: 'Server-Blogx', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''docker compose down
          
        sleep 40
    
        docker compose up -d''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '.env-commit,docker-compose.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      }
    }
  }
}
