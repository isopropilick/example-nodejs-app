pipeline {
    agent any
        environment {
            DEV_URL     = 'https://dev.ericpereyra.com/'
            QA_URL  = 'https://qa.ericpereyra.com/'
            STAGE_URL    = 'https://prod.ericpereyra.com/'
            DEV_PORT    = '8181'
            QA_PORT = '8182'
            STAGE_PORT   = '8183'
            JEN_TEST_URL= 'http://localhost:8080/jenkins/generic-webhook-trigger/invoke'
            //COMMKEY = credentials('commtok')
        }
    stages {
        stage('Clean env') {
            steps{
                script{
                    def names = ['ena-dev','ena-qa','ena-stage']
                    for (int i = 0; i < names.size(); i++){
                        containers = sh(script: "docker container ls -q -f name=\"${names[i]}\"", returnStdout: true).trim()
                        if (containers != ''){
                        echo "Container for ${names[i]} found with the ID: ${containers}, removing..."
                        sh "docker stop ${names[i]}"
                        }else{
                        echo "No running container for ${names[i]}."
                        }
                    }
                    //sh "docker builder prune -a -f"
                    sh "docker system prune -a -f"
                }
            }
        }
        stage('Lint Test') {
            steps {
                echo 'LTEST'
            }
        }
        stage('Unit Test') {
            steps {
                echo 'ITEST'
            }
        }
        stage('Integration Test'){
            steps{
                sh 'echo $PATH'
            }
        }
        stage('Dev e2e Test'){
            steps{
                parallel(
                    a: {
                        sh "npm install"
                        sh "npm run start:dev -- --port ${DEV_PORT}"
                    },
                    b: {
                        script {
                            //import groovy.json.JsonSlurper
                            hook = registerWebhook()
                            def response = httpRequest url:"${env.JEN_TEST_URL}",
                                customHeaders:[
                                    [ name:'Returnurl', value:"${hook.getURL()}"],
                                    [ name:'Testurl', value:"${env.DEV_URL}"],
                                    [ name:'Buildurl', value:"${env.BUILD_URL}"],
                                    [ name:'Buildid', value:"${env.BUILD_ID}"],
                                    [ name:'Buildnumber', value:"${env.BUILD_NUMBER}"]
                                    ],
                                    httpMode: 'POST',
                                    authentication:"eyJzdWIiOiIxMjNzZGE0NTY3ODkwIiwibmFtZSI6IkpvaG5hc2QgRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ"
                            println("Status: "+response.status)
                            data = waitForWebhook webhookToken:hook
                            //sh "rm -f -R allure-results"
                            //sh "mkdir allure-results"
                            //def root = readJSON text: data
                            //def keyList = root['files'].keySet()
                            //def filesmap = [:]
                            //println(keyList)
                            //for (String key : keyList){
                            //    filesmap[key] = root.files."${key}"
                            //    //println(filesmap[key])
                            //    writeJSON file: "allure-results/${key}", json:filesmap[key]
                            //}
                            
                            //sh "echo ${jsonObj.age}"   // prints out 5
                            //writeFile file: 'allure-results/TEST-DEV.test.xml', text: "${data}"
                            //println(data)
                            //sh "ls allure-results"
                            //archiveArtifacts artifacts: 'allure-results/*'
                            def quit = httpRequest url:"${env.DEV_URL}/quit", httpMode: 'POST'
                            //allure([
                            //    includeProperties: false,
                            //    jdk: '',
                            //    properties: [],
                            //    reportBuildPolicy: 'ALWAYS',
                            //    results: [[path: 'allure-results']]
                            //])
                        }
                    }
                )
            }
        }
        stage('QA e2e Test'){
            steps{
                parallel(
                    a: {
                        sh "npm install"
                        sh "npm run start:dev -- --port ${QA_PORT}"
                    },
                    b: {
                        script {
                            hook = registerWebhook()
                            def response = httpRequest url:"${env.JEN_TEST_URL}",
                                customHeaders:[
                                    [ name:'Returnurl', value:"${hook.getURL()}"],
                                    [ name:'Testurl', value:"${env.QA_URL}"],
                                    [ name:'Buildurl', value:"${env.BUILD_URL}"],
                                    [ name:'Buildid', value:"${env.BUILD_ID}"],
                                    [ name:'Buildnumber', value:"${env.BUILD_NUMBER}"]
                                    ],
                                    httpMode: 'POST'
                                println("Status: "+response.status)
                                data = waitForWebhook hook
                                def quit = httpRequest url:"${env.QA_URL}/quit", httpMode: 'POST'
                        }
                    }
                )
            }
        }
        stage('Stage e2e Test'){
            steps{
                parallel(
                    a: {
                        sh "npm install"
                        sh "npm run start:dev -- --port ${STAGE_PORT}"
                    },
                    b: {
                        script {
                            hook = registerWebhook()
                            def response = httpRequest url:"${env.JEN_TEST_URL}",
                                customHeaders:[
                                    [ name:'Returnurl', value:"${hook.getURL()}"],
                                    [ name:'Testurl', value:"${env.STAGE_URL}"],
                                    [ name:'Buildurl', value:"${env.BUILD_URL}"],
                                    [ name:'Buildid', value:"${env.BUILD_ID}"],
                                    [ name:'Buildnumber', value:"${env.BUILD_NUMBER}"]
                                    ],
                                    httpMode: 'POST'
                                println("Status: "+response.status)
                                data = waitForWebhook hook
                                def quit = httpRequest url:"${env.STAGE_URL}/quit", httpMode: 'POST'
                        }
                    }
                )
            }
        }
        stage('Build docker images'){
            steps{
                sh "docker build -t ena-dev-build ."
                sh "docker build -t ena-qa-build ."
                sh "docker build -t ena-stage-build ."
            }
        }
        stage('DEV docker deploy'){
            steps{
                sh "docker run --name ena-dev -p ${DEV_PORT}:8080 -d ena-dev-build"
            }
        }
        stage('QA docker deploy'){
            steps{
                sh "docker run --name ena-qa -p ${QA_PORT}:8080 -d ena-qa-build"
            }
        }
        stage('STAGE / PROD docker deploy'){
            steps{
                sh "docker run --name ena-stage -p ${STAGE_PORT}:8080 -d ena-stage-build"
            }
        }
    }
}
