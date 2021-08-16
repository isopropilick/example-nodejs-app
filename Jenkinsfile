pipeline {
    agent any
        environment {
            DEV_URL     = 'https://dev.ericpereyra.com'
            QA_URL  = 'https://qa.ericpereyra.com/'
            PROD_URL    = 'https://prod.ericpereyra.com/'
            DEV_PORT    = '8181'
            QA_PORT = '8182'
            PROD_PORT   = '8183'
            JEN_TEST_URL= 'https://ui.ericpereyra.com/jen/'
        }
    stages {
        stage('Build') {
            steps {
                echo 'TEST'
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
                sh "npm install"
                sh "npm run start:dev -- --port ${DEV_PORT}"
                script {
                    hook = registerWebhook()
                    def response = httpRequest url:"${env.JEN_TEST_URL}",
                                   customHeaders:[
                                       [ name:'Returnurl', value:"${hook.getURL()}"],
                                       [ name:'Testurl', value:"${env.DEV_URL}"],
                                       [ name:'Buildurl', value:"${env.BUILD_URL}"],
                                       [ name:'Buildid', value:"${env.BUILD_ID}"],
                                       [ name:'Buildnumber', value:"${env.BUILD_NUMBER}"]
                                   ]
                    println("Status: "+response.status)
                    data = waitForWebhook hook
                }
            }
        }

    }

}
