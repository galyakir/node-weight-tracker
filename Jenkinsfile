pipeline {

 agent {label 'aws'}
stages{

// this stage clean the environment
 stage ('checkout'){
   steps{
		cleanWs()
		checkout scm
        }
       }

// this stage download variables file for deploy of the app from S3
 stage ('S3download'){
   steps{
		 withAWS(region:'us-east-1',credentials:'awsCredentials')\
                  {
                      s3Download bucket: 'web-app-bucket-gal', file: 'var.txt', path: 'var.txt'
                  }
        }
       }
// this stage build the app with  deploy script
 stage ('build'){
   steps{
				  sh 'sudo apt install dos2unix'
				  sh 'dos2unix deploy.sh'
                  sh "chmod +x -R ${env.WORKSPACE}"
                  sh '(set -f; ./deploy.sh $(cat var.txt))'
        }
       }

// this stage archive the app files and upload them to S3
  stage ('archive and upload to S3 '){
   steps{
				  sh 'sudo apt install zip'
                  sh "zip node-weight-tracker.zip docs/* src/* tools/* .eslintrc.js LICENSE package-lock.json package.json"
				  withAWS(region:'us-east-1',credentials:'awsCredentials')\
                  {
					  s3Upload bucket: "web-app-bucket-gal/node-weight-tracker", workingDir:'', includePathPattern:'node-weight-tracker.zip'

                  }
                 archiveArtifacts artifacts: 'node-weight-tracker.zip', followSymlinks: false
        }
       }
 }
}

