pipeline {
agent { label 'AGENT' }

```
environment {
    PROJECT    = 'expense'
    COMPONENT  = 'backend'
    DEPLOY_TO  = 'Production'
    REGION     = 'us-east-1'
    CLUSTER    = 'expense-dev'
}

options {
    disableConcurrentBuilds()
    timeout(time: 30, unit: 'MINUTES')
}

parameters {
    string(
        name: 'version',
        defaultValue: '',
        description: 'Application Version from CI Pipeline'
    )
}

stages {

    stage('Validate Input') {
        steps {
            script {
                if (!params.version?.trim()) {
                    error("Version parameter is required")
                }

                echo "Deploying version: ${params.version}"
            }
        }
    }

    stage('Connect To EKS') {
        when {
            environment name: 'DEPLOY_TO', value: 'Production'
        }

        steps {
            withAWS(region: "${REGION}", credentials: 'aws-creds') {
                sh """
                    aws eks update-kubeconfig \
                        --region ${REGION} \
                        --name ${CLUSTER}

                    kubectl get nodes
                """
            }
        }
    }

    stage('Deploy Application') {
        steps {
            withAWS(region: "${REGION}", credentials: 'aws-creds') {
                sh """
                    kubectl set image deployment/backend \
                    backend=377426330809.dkr.ecr.us-east-1.amazonaws.com/expense/backend:${params.version} \
                    -n expense

                    kubectl rollout status deployment/backend -n expense
                """
            }
        }
    }
}

post {
    always {
        echo 'I Will Always say hello again'
        deleteDir()
    }

    success {
        echo 'Deployment completed successfully'
    }

    failure {
        echo 'Deployment failed'
    }
}
```

}
