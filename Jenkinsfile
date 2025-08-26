pipeline {
     agent any 
       tools {
               maven 'Maven3'
             }
        environments {
               REGISTRY = "docker.io"
               IMAGE_NAME = "server1"
              IMAGE_TAG = "latest"
             DOCKERHUB_CREDENTIALS = "creds"
            SERVER_PORT = "8085"
             }
           stages {
                 stage('checkout'){
                   steps{
                         git branch: 'main', url:'https://github.com/Curiousgoal202/26AUG.git'
                          }
                       }
                 stage('Build'){
                   steps{
                        sh 'mvn clean package'
                         }
                    }
                 stage('Test'){
                    steps{
                        sh 'mvn test'
                        }
                      }
                  stage('Code Quality Check'){
                    steps{
                        sh 'echo "No html linter configured"'
                           }
                        }
                  stage('Security Scan'){
                      steps{
                         sh 'docker run --rm -i hadolint/hadolint < Dockerfile || true'
                            }
                          }
                      stage('Build Docker Image'){
                        steps{
                            sh "docker built -t $IMAGE_NAME:$IMAGE_TAG ."
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
                       stage('Stop Old Container'){
                           steps {
                             sh """
                                 docker stop webserver || true
                                 docker rm webserver || true
                               """
                                }
                            }
                       stage('Run New Container'){
                             steps{
                                sh """
                                  docker run -d --name webserver -p $SERVER_PORT:80 $IMAGE_NAME:$IMAGE_TAG
                                 """
                               }
                            }
    stage('Health Check') {
            steps {
                script {
                    sh "sleep 5"
                    sh "curl -f http://localhost:$SERVER_PORT || exit 1"
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
                        
