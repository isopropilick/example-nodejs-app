pipeline {
    agent any
        environment {
            DEV_URL     = 'dev_url'
            SATAGE_URL  = 'stage_url'
            PROD_URL    = 'prod_url'
            DEV_PORT    = 'dev_port'
            SATAGE_PORT = 'stage_port'
            PROD_PORT   = 'prod_port'
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
                script {
                    hook = registerWebhook()
                    echo "${env.JEN_TEST_URL}"
                    def response = httpRequest url:'https://ui.ericpereyra.com/jen/',
                                   customHeaders:[
                                       [ name:'URL', value:'"${hook.getURL()}"'],
                                       [ name:'TARGET_URL', value:'"${env.DEV_URL}"']
                                   ]
                    println("Status: "+response.status)
                    data = waitForWebhook hook
                }
            }
        }

    }

}
