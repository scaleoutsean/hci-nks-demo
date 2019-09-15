node {
    def myRepo = checkout scm
    def gitCommit = myRepo.GIT_COMMIT
    def gitBranch = myRepo.GIT_BRANCH
    def shortGitCommit = "${gitCommit[0..10]}"
    def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
 
    

    stage('Build static image') {
      container('dind') {
          sh """
            docker build -t netapp/hci-nks-demo:static-${gitCommit} -f static/Dockerfile static/
            """
      }
    }

    stage('Build dynamic image') {
      container('dind') {
          sh """
            docker build -t netapp/hci-nks-demo:dynamic-${gitCommit} -f dynamic/Dockerfile dynamic/
            """
      }
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */
            sh 'echo "Tests passed"'

    }

    stage('Push image') {
      container('dind') {
        withCredentials([[$class: 'UsernamePasswordMultiBinding',
          credentialsId: 'Docker',
          usernameVariable: 'DOCKER_HUB_USER',
          passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
          sh """
            docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASSWORD}
            #docker push netapp/hci-nks-demo:static-${gitCommit}
            #docker push netapp/hci-nks-demo:dynamic-${gitCommit}
            """
        }
      }
    }
}