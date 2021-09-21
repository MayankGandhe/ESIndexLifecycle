pipeline {
    agent any
   parameters {
        booleanParam(name: 'Refresh', defaultValue: false, description: 'Refresh this Job')

        string(name: 'codeLocation', defaultValue: '/', description: '')
        string(name: 'bucketName', defaultValue: '', description: '')


    }
    stages {
        stage('Build') {
            agent {
                docker { image 'node' }
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
                      npm cache clean
                      npm run-script ng build
                      ls
                      """
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                  sh """
                    cd ${params.codeLocation}/dist
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
