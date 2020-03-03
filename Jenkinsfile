pipeline {
   agent {
       label 'master'
   }
   stages {
      stage('Build') {
         steps {
            sh "echo Hello World. Hurry!!!! triggered this job on a git push to the webhook."
         }
      }
   }
}
