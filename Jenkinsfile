node {
    stage('Build') {
        agent {
            docker { image 'node:16-alpine' }
        }
        steps {
            sh 'node --version'
        }
    }
}