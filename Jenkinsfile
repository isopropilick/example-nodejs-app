pipeline {
    agent any
        environment {
            DEV_URL     = 'dev_url'
            SATAGE_URL  = 'stage_url'
            PROD_URL    = 'prod_url'
            DEV_PORT    = 'dev_port'
            SATAGE_PORT = 'stage_port'
            PROD_PORT   = 'prod_port'
            JEN_TEST_URL= '\'https://99065421-f33e-4b17-9576-e1b9cb01a687.mock.pstmn.io\''
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
                    def response = httpRequest url:env.JEN_TEST_URL,
                                   customHeaders:[
                                        [ name: 'URL', value: hook.getURL()],
                                        [ name: 'TARGET_URL', value: env.DEV_URL]
                                   ],
                    data = waitForWebhook hook
                }
            }
        }

    }

}
