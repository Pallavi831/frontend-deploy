pipeline {
    agent {
        label 'AGENT-1'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    environment {
        // DEBUG = 'true'
        appVersion = '' // Global variable, set in first stage
        region = 'us-east-1'
        account_id = '557690626059'
        project = 'expense'
        component = 'frontend'
        environment = ''
        DEPLOY_TO = "production"
    }
    parameters{
         string(name: 'version',  description: 'Enter the application version')
         choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick something')
    }
    stages {

        stage('Setup Environment') {
            steps {
                script {
                    appVersion = params.version
                    environment = params.deploy_to
                }
            }
        }

        stage('Deploy') {
            steps {
                 script{
                    withAWS(region: "${region}", credentials: "aws-creds") {
                        sh """
                        export PATH=$PATH:/usr/local/bin
                        aws eks update-kubeconfig --region ${region} --name ${project}-${environment}
						kubectl get nodes
                        cd helm
                        sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml
                        helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .

                        """
                    }

                }
                

            }
        }   
    }
    post {
        always {
            echo "This section runs always"
            deleteDir()
        }
        success {
            echo "This section runs when the pipeline succeeds"
        }
        failure {
            echo "This section runs when the pipeline fails"
        }
    }
}
