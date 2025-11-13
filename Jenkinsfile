pipeline {
    // agent none
     environment {
        DOCKERHUB_USERNAME = credentials('dockerhub_creds') // Jenkins credentials ID
        DOCKERHUB_PASSWORD = credentials('dockerhub_creds') // Jenkins credentials ID
    }
    stages {
        stage('Build Java Artifact') {
            agent any  // Run on the master node
            steps {
                script {
                    // Assuming a Maven project
                    dir('/home/shivakumar76712/spring-petclinic') {  // Update to your project path
                        sh 'mvn clean package' // Build the project
                        stash name: 'jar-file' , target: 'target/*.jar'
                    }
                }
            }
        }
        stage('Build Docker Image') {
            agent { 
                label 'java-slave'
                }                     // Run on the slave node
            steps {
                script {

                    // unstash  the jar file
                    unstash 'jar-file'

                    // Copy  Dockerfile to the slave node
                    sh '''
                    cp  /home/shiva/target   home/shiva/jenkinshome/
                    cp  /home/shivakumar76712/spring-petclinic/Dockerfile  /home/shiva/jenkinshome/
                    '''
                    // Build the Docker image
                    dir('/home/shiva/jenkinshome/') {  // Update to your destination path
                        sh 'docker build -t shiva261/sprimg:v1 .'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            agent { 
                label 'slave'
                }                    
            steps {
                script {
                    // Log in to Docker Hub
                    sh 'echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin'
                    
                    // Push the Docker image
                    sh 'docker push  shiva261/sprimg:v1'
                }
            }
        }
    }
   
}

