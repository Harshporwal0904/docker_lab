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
    }
    post{
        always{
            bat "docker rmi %IMAGE%:%TAG%"
        }
    }

}