pipeline {
    agent any

    stages {
        stage('Get Source') {
            steps {
                echo 'Get pipeline...'
                dir('pipeline') {
                    git url: 'https://github.com/CIL-Rio/ci-cd-pipeline.git', branch: 'main'
                }
                echo 'Get Frontend...'
                dir('frontend') {
                    git url: 'https://github.com/CIL-Rio/front-end.git', branch: 'master'
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Docker Build...'
                dir('frontend') {
                    script {
                        dockerapp = docker.build("leandroschwab/ciscoshop-frontend:${env.BUILD_ID}",
                            '-f ./Dockerfile .')
                    }
                }
            }
        }

        stage('Docker Push Image') {
            steps {
                echo 'Docker Push Image...'
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy Kubernetes') {
            agent {
                kubernetes {
                    cloud 'kubernetes'
                }
            }
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                echo 'Deploy Kubernetes...'
                dir('pipeline') {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./kubernetes/api.yaml'
                    sh 'cat ./kubernetes/api.yaml'
                    kubernetesDeploy(configs: '**/kubernetes/**', kubeconfigId: 'kubeconfig')
                }
            }
        }
    }
}
