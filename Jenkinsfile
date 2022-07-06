pipeline {
    
    agent any
    stages {
        stage('Clone repository') {               
            steps {
                checkout scm 
            }
        }
        stage ('Build & Push ') {
            steps {
                echo 'Building'
                sh 'ls -ltra'
                script {
                    def app
                    app = docker.build("safficodefresh/test-report-image-jenkins")
                    // require credentials to be stored under DOCKERHUB
                    docker.withRegistry('https://registry.hub.docker.com', 'DOCKERHUB') {            
                            app.push("${env.BUILD_NUMBER}")            
                            app.push("latest")        
                    }
                }
                script { // check
                    docker.pull("safficodefresh/test-report-image-jenkins:${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('call-report') {
            environment {
                CF_ENRICHERS = 'jira'
                CF_BRANCH = 'main'
                CF_HOST = 'https://saffi.pipeline-team.cf-cd.com'
                CF_API_KEY = credentials('codefresh-token')
                CF_IMAGE = "safficodefresh/test-report-image-jenkins:${env.BUILD_NUMBER}"
                CF_CONTAINER_REGISTRY_INTEGRATION= 'docker'
                CF_JIRA_INTEGRATION= 'jira'
                CF_JIRA_MESSAGE= '''
                        A message with embedded issue ( i.e. CR-11027 )
                        that would be use query jira for the ticket '''
                CF_JIRA_PROJECT_PREFIX = 'CR'
                CF_WORKFLOW_NAME = "${env.JOB_NAME}"
                CF_WORKFLOW_URL = "${env.JOB_URL}"
                CF_VERBOSE = "true"
//                 CF_GIT_REPO = "myRepo"
            }
            agent {
                docker { 
                    registryUrl 'https://quay.io'
                    registryCredentialsId 'quay-id'
                    image "quay.io/codefresh/codefresh-report-image:0.0.61"
                    args "--net='host'"
                }
            }
//             stages {
//                 stage('report-image') {
                    steps {
                        sh 'node --version'
                        sh 'ls -ltra'
                        sh 'cd /code && yarn start'
//                     }
//                 }
            }
        }
        
    }
}
