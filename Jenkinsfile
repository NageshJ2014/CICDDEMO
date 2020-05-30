pipeline {
   agent any
   environment {
       registry = "njdocker2014/cicddemo"
       GOCACHE = "/tmp"
      /* appImage = ${registry} + "$BUILD_NUMBER" */
   }
   stages {
      /* stage('Build') {
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
       }*/
      stage('K8s Connect') {
         environment {
            ServiceAccount = '${WORKSPACE}/burnished-case-244609-ecddde5f5747.json'
         }
         steps {
            script{
               sh ' echo ${ServiceAccount} --'
               sh ' echo $WORKSPACE/burnished-case-244609-ecddde5f5747.json '
               sh ' gcloud auth  activate-service-account --key-file=${WORKSPACE}/burnished-case-244609-ecddde5f5747.json '
               sh ' gcloud config set project burnished-case-244609 '
               sh ' gcloud container clusters get-credentials nj-cluster --zone us-central1-c --project burnished-case-244609 '
            }
         }
      }
     /* stage('Deploy to K8S'){
         steps {
            script{              
               
               
               def appImage = "njdocker2014/cicddemo:28"
               
               sh ' echo -- ${appImage} -- '
               sh ' echo -- $appImage -- '
               sh ' if [[ `k get svc hello-svc > /dev/null 2>&1` ]] ; then echo "Service Not Found"; else echo "Service Found, Deleting "; kubectl delete -f service.yml; fi'
               
               sh 'sed "s|{{GO_HELLO_APP}}|${appImage}|" hello-app.yml > hello_app1.yml'               
               sh 'kubectl create -f service.yml > service.log 2>&1 '
                sh 'cat service.log' 
               sh 'kubectl create -f hello_app1.yml > app.log 2>&1 '
                sh 'cat app.log' 
               
            }
         }
      }
*/
      
       stage ('Deploy') {
           steps {
               script{
                   def appImage = "njdocker2014/cicddemo:28"
                   def image_id = registry + ":$BUILD_NUMBER"
                   echo "-- appImage = $appImage --"
                   echo " Printing AppImage = ${appImage} "
                   
                   echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                   echo " $image_id "
                  echo "-- ${image_id} --"
                  sh "sed -i -e 's|{{GO_HELLO_APP}}|${image_id}|g' hello-app.yml"
                   sh 'sed -i \"s|{{GO_HELLO_APP}}|${image_id}|\" hello-app.yml > hello_app1.yml' 
                  sh ' grep -i image hello_app1.yml '
                   /* sh "ansible-playbook  playbook.yml --extra-vars \"image_id=${image_id}\"" */
               }
           }
       } 
   }
}
