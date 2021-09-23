pipeline {
    agent any
     environment {
    AZURE_SUBSCRIPTION_ID='161758cf-d810-4cf2-b377-e298c3720301'
    AZURE_TENANT_ID='e88709d3-794e-40c8-b19b-66e6b066ef55'
    AZURE_STORAGE_ACCOUNT='frontend'
  }
   parameters {
        booleanParam(name: 'Refresh', defaultValue: false, description: 'Refresh this Job')

        string(name: 'codeLocation', defaultValue: '/', description: '')
        string(name: 'containerName', defaultValue: 'frontendweb', description: '')


    }
    stages {
                stage('Deploy') {
            steps {
                
                echo 'Deploying....'
                         withCredentials([usernamePassword(credentialsId: 'azuresp', 
                          passwordVariable: 'AZURE_CLIENT_SECRET', 
                                                           usernameVariable: 'AZURE_CLIENT_ID')]) {
                  sh """
              /root/bin/az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
              # Set default subscription
              /root/bin/az account set --subscription $AZURE_SUBSCRIPTION_ID
              # Execute upload to Azure
              /root/bin/az storage blob upload-batch --destination ${params.containerName} --source . --account-name $AZURE_STORAGE_ACCOUNT
              # Logout from Azure
              /root/bin/az logout                  """
                         }
            }
        }
        stage('Build') {
            agent {
                docker { image 'trion/ng-cli' }
            }
                environment {
        HOME = '.'
    }
            steps {
                sh """
                      echo ${params.codeLocation}                      
                      cd ${params.codeLocation}
                      ls
                      npm cache clean --force
                      npm install
                      npm install @angular/cli
                      npm run-script ng build
                      ls
                      """
            }
        }

    }
        post {
    cleanup {
        cleanWs(cleanWhenFailure: false)
    }
    }
}
