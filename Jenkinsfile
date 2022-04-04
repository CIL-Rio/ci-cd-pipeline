pipeline{
    agent any

    stages{

        stage('Get Source'){
            steps{
                echo 'Get Sources...'
                dir('frontend'){
                    git url: 'https://github.com/CIL-Rio/front-end.git', branch: 'master'
                }
            }
        }

        stage('Docker Build'){
            steps{
                echo 'Docker Build'
                dir('frontend'){
                    script{
                        dockerapp = docker.build("leandroschwab/ciscoshop-frontend:${env.BUILD_ID}",
                            '-f /Dockerfile .')
                    }
                } 
            }
        }

        stage('Docker Push Image'){
            steps{
                echo 'Docker Push Image...'
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