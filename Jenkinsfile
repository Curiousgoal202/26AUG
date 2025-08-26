pipeline {
     agent any 
       tools {
               maven 'Maven3'
             }
environment {
    REGISTRY = "docker.io"
    IMAGE_NAME = "server1"
    IMAGE_TAG = "latest"
    DOCKERHUB_CREDENTIALS = "creds"
    SERVER_PORT = "8085"
}
           stages {
                 stage('checkout'){
                   steps{
                         git branch: 'master', url:'https://github.com/Curiousgoal202/26AUG.git'
                          }
                       }
   
                      stage('Build Docker Image'){
                        steps{
                            sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
                           }
                          }
                       stage('Push Docker Image'){
                          steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker tag $IMAGE_NAME:$IMAGE_TAG $DOCKER_USER/$IMAGE_NAME:$IMAGE_TAG
                        docker push $DOCKER_USER/$IMAGE_NAME:$IMAGE_TAG
                        """
                    }
                }
            }
        }
                 stage('Run New Container') {
    steps {
        script {
            sh """
                # Stop and remove old container if exists
                docker ps -a -q --filter name=webserver | grep -q . && docker stop webserver && docker rm webserver || true

                # Run new container
                docker run -d --name webserver -p 8085:80 server1:latest
            """
        }
    }
}

            
    }

    post {
        success {
            echo "✅ Deployment successful!"
            mail to: 'santosgoal2024@gmail.com',
                 subject: "SUCCESS: Webserver Pipeline",
                 body: "Your webserver is up on port $SERVER_PORT"
        }
        failure {
            echo "❌ Deployment failed!"
            mail to: 'santosgoal2024@gmail.com',
                 subject: "FAILED: Webserver Pipeline",
                 body: "Please check the Jenkins logs for details."
        }
    }
}
                        
