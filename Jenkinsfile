node {
    checkout scm
    shortCommit = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
    docker.image('python:2-alpine').inside {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    docker.image('qnib/pytest').inside {
        stage('Test') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
    stage('Manual Approval') {
        input(message: "Lanjutkan ke tahap Deploy?")
    }
    stage('Deploy') {
        sh(label: 'menjeda pipeline selama 20 detik', script: 'sleep 20')
        docker.withRegistry('https://registry.hub.docker.com', 'docker-yusuf') {
            def customImage = docker.build("auzty/dicoding-submission:${shortCommit}")
            /* Push the container to the custom Registry */
            customImage.push()
        }
        //input(message: "sudahkan anda selesai?")
    }
}