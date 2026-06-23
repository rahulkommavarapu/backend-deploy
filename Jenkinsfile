pipeline {
    agent { label 'AGENT' }

    environment {
        PROJECT = 'EXPENSE'
        COMPONENT = 'BACKEND'
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
        booleanParam(
            name: 'TOGGLE', defaultValue: true, description: 'Toggle this value'
            choice(name: 'deploy_to',defaultValue: 'dev',choices: ['dev', 'qa', 'prod'], description: 'Pick something')
        )
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
                            aws eks update-kubeconfig --region ${REGION} --name expense-dev
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml 
                            cat values-${environment}.yaml
                        """
                    }
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