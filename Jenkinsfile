

//  4) Create the Java artifact on the master node,
//     then use a slave node to create the Docker image and push it to Docker Hub.

pipeline {
    agent none
    environment {
        DOCKERHUB_USERNAME = credentials('dockerhub_creds')
        DOCKERHUB_PASSWORD = credentials('dockerhub_creds')
    }
    stages {
        stage('Build Java Artifact') {
            agent any
            steps {
                script {
                    dir('/home/shivakumar76712/spring-petclinic') {
                        sh 'mvn clean package'
                        // FIXED: Use 'includes' instead of 'target'
                        stash name: 'jar-file', includes: 'target/*.jar'
                    }
                }
            }
        }
        stage('Build Docker Image') {
            agent { 
                label 'java-slave'
            }
            steps {
                script {
                    // Unstash the jar file
                    unstash 'jar-file'

                    // Copy Dockerfile to the slave node
                    sh '''
                        cp /home/shivakumar76712/spring-petclinic/Dockerfile /home/shiva/jenkinshome/
                        cp target/*.jar /home/shiva/jenkinshome/
                    '''
                    
                    // Build the Docker image
                    dir('/home/shiva/jenkinshome/') {
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
                    sh 'docker push shiva261/sprimg:v1'
                }
            }
        }
    }
}

    

   
