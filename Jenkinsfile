pipeline {
    agent { label 'AGENT' }

    environment {
        PROJECT = 'expense'
        COMPONENT = 'backend'
        DEPLOY_TO = 'Production'
        REGION = 'us-east-1'
        appVersion = ''
        environment = ''
    }

    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }
    parameters {
            string(name: 'version', description: 'Enter the application version')
            choice(name: 'deploy_to',choices: ['dev', 'qa', 'prod'], description: 'Pick something')
    }
    stages {

        stage ('Setup Environment')  {
            steps {
                script{
                    appVersion = params.version
                    environment = params.deploy_to
                }
                 
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                        sh """
                            aws eks update-kubeconfig --region $REGION --name expense-${environment}
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml 
                            helm upgrade --install $COMPONENT -n $PROJECT -f values-${environment}.yaml .
                        """
                    }
                }
            }
        }
        stage('Funtional Tests'){
            when{
                   expression {params.deploy_to == 'dev'}
            }
        steps{
            script{
                sh """
                echo 'Functional tests will be performed after DEV deployment useually this are automated by Selenium test cases Written by Testing Team. if this test Cases are Failed  Pipeline also Faile'
                """
            }
        }

        }
        
        stage('Integration Tests'){
            when{
                   expression {params.deploy_to == 'qa'}
            }
        steps{
            script{
                sh """
                echo 'Integration tests will be performed after QA,UAT deployment useually this are automated by BDD(Behaviour Driven Devlopment) test cases in cucumber framework Testing Team. if this test Cases are Failed  Pipeline also Faile'
                """
            }
        }

        }

        stage('Parallel Stage') {
            steps {
                echo "Parallel Stage"
            }
        }
    }

    post {
        always {
            echo 'I Will Always say hello again'
        }

        failure {
            echo 'I will run when Pipeline is Failed'
        }

        success {
            echo 'I will run when pipeline is success'
        }
    }
}