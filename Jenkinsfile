pipeline {
  agent any
    
  tools {nodejs "node"}
     
    stage('Build') {
      steps {
        sh 'npm install'
         sh 'npm build'
      }
    } 
  }
