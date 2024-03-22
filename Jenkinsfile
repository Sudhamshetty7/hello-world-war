pipeline {
    agent { label 'slave-1' }
    stages {
        stage('Checkout') {
            steps {
		sh 'rm -rf  hello-world-war'
                sh 'git clone https://github.com/Sudhamshetty7/hello-world-war.git'
            }
        }
	stage('Build') {
            steps {
                sh 'echo "inside build stage"'
                dir("hello-world-war") {    
                    sh 'docker build -t tomcat-file:${BUILD_NUMBER} .'
                }
            }
        }
        stage('Push Image to Docker Hub') {         
            steps { 
                withCredentials([usernamePassword(credentialsId: 'credentials', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                sh "docker login -u $USERNAME -p $PASSWORD"
                sh "docker tag tomcat-file:${BUILD_NUMBER} sudham07/jeniks:${BUILD_NUMBER}"
                sh "docker push sudham07/jeniks:${BUILD_NUMBER}"                 
                echo 'Image pushed to registry'       
            }           
        }
        }
        stage('Deploy') {
            parallel {
                stage('Deploy to slave-2') {
                    agent { label 'slave-2' }
                    steps {
                        sh "docker pull sudham07/jeniks:${BUILD_NUMBER}"
                        sh "docker run -d --name container-1 -p 8080:8080 sudham07/jeniks:${BUILD_NUMBER}"
                    }
                }
                stage('Deploy to slave3') {
                    agent { label 'slave-3' }
                    steps {
                        sh "docker pull sudham07/jeniks:${BUILD_NUMBER}"
                        sh "docker run -d --name container-2 -p 8080:8080 sudham07/jeniks:${BUILD_NUMBER}"
                    }
                }
            }
       }
   }
}
