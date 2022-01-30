pipeline {
  environment {
    registry = "saragoza68/spring-petclinic-hub"
    registryCredential = 'dockerHub'
    dockerImage = ''
  }
  agent any
  tools {
    maven 'MAVEN_3_8'
  } 
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/mklmfane/spring-petclinic-jenkins-pipeline.git'
      }
    }
    stage('Compile') {
       steps {
         sh 'mvn compile' //only compilation of the code
       }
    }
    stage('Test') {
      steps {
        sh '''
        mvn clean install
        ls
        pwd
        ''' 
        //if the code is compiled, we test and package it in its distributable format; run IT and store in local repository
      }
    }
    stage('Building Image') {
      steps{
        script {
          dockerImage = docker.build registry + ":latest"
        }
      }
    }
    
    stage('Analyze with Anchore plugin') {
      steps {
         writeFile file: 'anchore_images', text: registry
         anchore name: 'anchore_images'
      }
    }
    
    stage('Deploy Image') {
      steps{
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:latest"
      }
    }
  }
}
