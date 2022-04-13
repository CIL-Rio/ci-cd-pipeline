pipeline {
    agent any

    stages {
        stage('Get Source') {
            steps {
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
        
        stage('SecureCN Vunerability Scan') {
            steps {
                echo 'Secure CN Vunerability Scn'
                secureCNVulnerabilityScanner(imageName:"leandroschwab/ciscoshop-frontend:${env.BUILD_ID}", secureCnAccessKey:'c1ac9ea8-3f09-470a-8e8b-c189b4c9cf82', secureCnSecretKeyId:'securecn-secret',highestSeverityAllowed: 'CRITICAL', highestSeverityAllowedDf:'FATAL', url: 'securecn.cisco.com', pushLocalImage: 'true')                        
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
                //sh "ls -la ${pwd()}"
                sh 'sed -i "s/{{tag}}/$tag_version/g" complete-demo.yaml'
                //sh 'cat complete-demo.yaml'
                kubernetesDeploy(configs: 'complete-demo.yaml', kubeconfigId: 'kubeconfig')
            }
        }
    }
}
