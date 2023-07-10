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
        sshPublisher(publishers: [sshPublisherDesc(configName: 'Server-Blogx', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''export GIT_COMMIT_SHORT=$(echo $GIT_COMMIT | head -c 7)
        echo "GIT_COMMIT_SHORT=$(echo $GIT_COMMIT_SHORT)" > .env
        docker compose pull
        docker compose up -d --remove-orphans
        docker image prune -f''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'docker-compose.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
      }
    }
  }
}
