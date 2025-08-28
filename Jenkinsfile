pipeline {
    agent any

    environment {
        DOCKER_REGISTRY_USER = 'floryos'
        DOCKER_IMAGE_NAME    = 'devops-application'
    }

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    stages {

       stage('SCM GitHub') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/celalettinaksoy/devops-02-pipeline']])
            }
        }

        stage('Test Maven') {
            steps {
            //    sh 'mvn test'
                bat 'mvn test'
            }
        }


        stage('Build Maven') {
            steps {
            //    sh 'mvn clean install'
                bat 'mvn clean install'
            }
        }





        stage('Docker Image Build') {
            steps {
            //    sh 'docker build -t ${DOCKER_REGISTRY_USER}/${DOCKER_IMAGE_NAME}:latest .'
                bat 'docker build -t ${DOCKER_REGISTRY_USER}/${DOCKER_IMAGE_NAME}:latest .'
            }
        }


        stage('Docker Image To DockerHub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {

                        if (isUnix()) {
                             sh 'docker login -u ${DOCKER_REGISTRY_USER} -p ${DOCKER_TOKEN}'
                             sh 'docker push ${DOCKER_REGISTRY_USER}/${DOCKER_IMAGE_NAME}:latest'
                          } else {
                             bat 'docker login -u ${DOCKER_REGISTRY_USER} -p ${DOCKER_TOKEN}'
                             bat 'docker push ${DOCKER_REGISTRY_USER}/${DOCKER_IMAGE_NAME}:latest'
                         }
                    }
                }
            }
        }




        stage('Deploy Kubernetes') {
            steps {
            script {
                    kubernetesDeploy (configs: 'deployment-service.yaml', kubeconfigId: 'kubernetes')
                }
            }
        }


        stage('Docker Image to Clean') {
            steps {

                   //  sh 'docker image prune -f'
                     bat 'docker image prune -f'

            }
        }



    }

}