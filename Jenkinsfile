pipeline {
    agent { label 'AGENT' }

    environment {
        PROJECT = 'expense'
        COMPONENT = 'backend'
        DEPLOY_TO = 'Production'
        REGION = 'us-east-1'
    }

    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }

    parameters {
        string(name: 'version', description: 'Enter the application Version')
    }

    stages {

        stage('Deploy') {

            when {
                environment name: 'DEPLOY_TO', value: 'Production'
            }

            steps {
                script {
                    withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                        sh """
                            aws eks update-kubeconfig --region ${REGION} --name expense-dev
                            kubectl get nodes
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
