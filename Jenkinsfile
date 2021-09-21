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
                      ls
                      """
            }
        }
        stage('Upload ') {
                      agent {
                docker { image 'trion/ng' }
            }
                environment {
        HOME = '.'
    }
            steps {
                
                echo 'Testing..'
                sh """
                    cd ${params.codeLocation}
                    ng build
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
