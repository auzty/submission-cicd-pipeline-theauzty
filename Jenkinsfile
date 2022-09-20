node {
    checkout scm
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
    stage('Deploy') {
        shortCommit = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
        sh 'docker build -t myimg .'
        sh 'echo "menjeda pipeline selama 20 detik"; sleep 20'
        sh "echo ${ shortCommit }"
        //input(message: "sudahkan anda selesai?")
    }
}