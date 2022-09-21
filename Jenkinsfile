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
        // run the ssm command for deploying to ec2
        def envArgs = "-e AWS_ACCESS_KEY_ID=${credentials('AWS_ACCESS_KEY_ID')} -e AWS_SECRET_ACCESS_KEY=${credentials('AWS_SECRET_ACCESS_KEY')} -e AWS_DEFAULT_REGION=ap-southeast-1"
        docker.image('amazon/aws-cli').withRun("${envArgs}"){
            sh 'ssm send-command --instance-ids i-0c39c6cd8974fa775 --document-name "AWS-RunShellScript" --parameters \'{"commands": ["#!/bin/bash","docker rm -f dicodingsubmission","docker run --name dicodingsubmission -d ${shortCommit}"] }\''
        }

        //input(message: "sudahkan anda selesai?")
    }
}