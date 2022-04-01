pipeline{
    agent any

    stages{

        stage('Get Source'){
            steps{
                dir('frontend')
                git url: 'https://github.com/CIL-Rio/front-end.git', branch: 'main'

            }
        }

        stage('Docker Build'){
            steps{
                script{
                    dockerapp = docker.build("leandroschwab/ciscoshop-frontend:${env.BUILD_ID}",
                        '-f ./frontend/Dockerfile .')
                } 
            }
        }

        stage('Docker Push Image'){
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                    }
                } 
            }
        }
    }
}