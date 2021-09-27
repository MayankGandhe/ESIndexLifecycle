pipeline {
    agent any
     environment {
    AZURE_SUBSCRIPTION_ID='161758cf-d810-4cf2-b377-e298c3720301'
    AZURE_TENANT_ID='e88709d3-794e-40c8-b19b-66e6b066ef55'
    AZURE_STORAGE_ACCOUNT='gokloudfrontend'
  }
   parameters {
        booleanParam(name: 'Refresh', defaultValue: false, description: 'Refresh this Job')
        booleanParam(name: 'ProvisionInfra', defaultValue: false, description: 'Will create Storage account and Blob')

        string(name: 'codeLocation', defaultValue: '/', description: '')
        string(name: 'containerName', defaultValue: 'frontendweb', description: '')


    }
    stages {
        stage('Infra Provision') {
            steps {
                
                echo 'Deploying....'
withCredentials([azureServicePrincipal('azurecred')]) 
                
{
                  sh """
                  ls
              /root/bin/az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
              # Set default subscription
              /root/bin/az account set --subscription $AZURE_SUBSCRIPTION_ID
              ls
              pwd
              /root/bin/az deployment group create --name addstorage --resource-group frontend --template-file StaticWebsiteHosting.arn
              # Logout from Azure
              /root/bin/az logout                  """
                         }
            }
        }
         stage('Build') {
            agent {
                docker { image 'trion/ng-cli'
                          reuseNode true
                  //  args '-v '${params.codeLocation}:/usr/share/maven/ref/repository/'
}
            }
                environment {
        HOME = '.'
    }
            steps {
                sh """
                      echo ${params.codeLocation}                      
                      cd ${params.codeLocation}
                      ls
                      pwd
                      npm cache clean --force
                      npm install
                      npm install @angular/cli
                      npm run-script ng build
                      ls
                      """
            }
        }

   stage('Deploy') {
            steps {
                
                echo 'Deploying....'
withCredentials([azureServicePrincipal('azurecred')]) 
                
{
                  sh """
                  ls
              /root/bin/az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
              # Set default subscription
              /root/bin/az account set --subscription $AZURE_SUBSCRIPTION_ID
              cd ${params.codeLocation}
              ls
              pwd
              /root/bin/az storage blob upload-batch --destination ${params.containerName} --source ./dist --account-name $AZURE_STORAGE_ACCOUNT
              # Logout from Azure
              /root/bin/az logout                  """
                         }
            }
        }

        
       
    }
    
        post {
    cleanup {
        cleanWs(cleanWhenFailure: false)
    }
    }
}
