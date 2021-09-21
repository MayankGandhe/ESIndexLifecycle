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
        string(name: 'bucketName', defaultValue: '', description: '')


    }
    stages {
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
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                  sh """
                    cd dist
                    aws s3 cp . s3://${params.bucketName}/ --recursive
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
