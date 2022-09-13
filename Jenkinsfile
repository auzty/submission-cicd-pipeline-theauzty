node {
    stage('Build') {
        agent {
            docker { image 'node:16-alpine' }
        }
        sh 'node --version'
    }
}