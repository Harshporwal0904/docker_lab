pipeline{
    agent any

    environment{
        IMAGE = "harshporwal01/flask-app"
        TAG = "v${BUILD_NUMBER}"
    }
    
    stages{
        stage('Checkout'){
            steps{
                checkout scm
            }
        }

        stage('Building Image'){
            steps{
                bat "docker build -t %IMAGE%:%TAG% ."
                bat "docker tag %IMAGE%:%TAG% %IMAGE%:latest"
            }
        }

        stage('Pushing Image'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: "dockerhub-creds",
                    usernameVariable:"DOCKER_USER",
                    passwordVariable:"DOCKER_PASS"
                )])
                {
                    bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin" 
                    bat "docker push %IMAGE%:%TAG%"
                    bat "docker push %IMAGE%:latest"
                }
            }
        }
        // stage('Deploy to EC2') {
        //     steps {
        //         sshagent(['ec2-key']) {
        //     sh '''
        //     ssh -o StrictHostKeyChecking=no ec2-user@EC2-IP "
        //     docker pull harshporwal01/nginx-app:latest &&
        //     docker stop nginx-container || true &&
        //     docker rm nginx-container || true &&
        //     docker run -d --name nginx-container -p 80:80 harshporwal01/nginx-app:latest
        //     "
        //     '''
        //         }
        //     }
        // }
    }
    post{
        always{
            bat "docker rmi %IMAGE%:%TAG%"
        }
    }

}