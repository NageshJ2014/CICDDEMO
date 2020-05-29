appImage = ""
pipeline {
   agent any
   environment {
       registry = "njdocker2014/cicddemo"
       GOCACHE = "/tmp"
   }
   stages {
       stage('Build') {
           agent {
               docker {
                   image 'golang'
               }
           }
           steps {
               // Create our project directory.
               sh 'cd ${GOPATH}/src'
               sh 'mkdir -p ${GOPATH}/src/hello-world'
               // Copy all files in our Jenkins workspace to our project directory.               
               sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
               // Build the app.
               sh 'go build'              
           }    
       }
       stage('Test') {
           agent {
               docker {
                   image 'golang'
               }
           }
           steps {                
               // Create our project directory.
               sh 'cd ${GOPATH}/src'
               sh 'mkdir -p ${GOPATH}/src/hello-world'
               // Copy all files in our Jenkins workspace to our project directory.               
               sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
               // Remove cached test results.
               sh 'go clean -cache'
               // Run Unit Tests.
               // sh 'go test ./... -v -short'           
              sh 'go test ./*_test.go -v -short'
           }
       }
       
       stage('Publish') {
           environment {
               registryCredential = 'DockerNJ'
           }
           steps{
               script {
                   appimage = docker.build registry + ":$BUILD_NUMBER"
                   docker.withRegistry( '', registryCredential ) {
                       appimage.push()
                       appimage.push('latest')
                   }
               }
           }
       }
      stage('K8s Connect') {
         environment {
            ServiceAccount = '${WORKSPACE}/burnished-case-244609-ecddde5f5747.json'
         }
         steps {
            script{
               sh ' echo $WORKSPACE/burnished-case-244609-ecddde5f5747.json '
               sh ' gcloud auth  activate-service-account --key-file=${WORKSPACE}/burnished-case-244609-ecddde5f5747.json '
               sh ' gcloud config set project burnished-case-244609 '
               sh ' gcloud container clusters get-credentials nj-cluster --zone us-central1-c --project burnished-case-244609 '
            }
         }
      }
      stage('Deploy to K8S'){
         steps {
            script{
               sh 'sed "s|{{GO_HELLO_APP}}|$appimage|" hello-app.yml > hello_app1.yml'
               sh 'kubectl create -f service.yml '
               /* sh 'cat service.log' */
               sh 'kubectl create -f hello_app1.yml'
               /* # sh 'cat app.log' */
               
            }
         }
      }
      /*
       stage ('Deploy') {
           steps {
               script{
                   def image_id = registry + ":$BUILD_NUMBER"
                   sh "ansible-playbook  playbook.yml --extra-vars \"image_id=${image_id}\""
               }
           }
       } */
   }
}
