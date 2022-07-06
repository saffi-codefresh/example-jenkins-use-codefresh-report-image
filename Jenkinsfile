pipeline {
    
    agent any
    stages {
        stage('Clone repository') {               
            steps {
                checkout scm 
            }
        }
        stage ('Build') {
            steps {
                echo 'Hello World'
                sh 'ls -ltra'
                script {
                    def app
                    app = docker.build("codefresh-io/example-jenkins-use-codefresh-report-image")    
                    docker.withRegistry('https://registry.hub.docker.com', 'git') {            
                            app.push("${env.BUILD_NUMBER}")            
                            app.push("latest")        
                    }   
                }
            }
        }
        stage('pull/run image') {           
            app.inside {            
                sh 'echo "check you can pull / run"'        
            }    
        }  
        
        stage('call-report') {
            environment {
                // CF_ENRICHERS = 'jira'
                // CF_BRANCH = 'PS-15'
                CF_HOST = 'https://saffi.pipeline-team.cf-cd.com'
                CF_IMAGE = 'safficodefresh/test-report-image-jenkins:0.0.1'
                CF_CONTAINER_REGISTRY_INTEGRATION= 'docker'
                // CF_JIRA_INTEGRATION= 'jira'
                CF_API_KEY = credentials('codefresh-token')
                CF_JIRA_MESSAGE= "A message with embedded issue ( i.e. CR-11027 ) that would be use query jira for the ticket "
                CF_JIRA_PROJECT_PREFIX = 'CR'
                CF_WORKFLOW_NAME = "${env.JOB_NAME}"
                CF_WORKFLOW_URL = "${env.JOB_URL}"
                CF_VERBOSE = "true"
                CF_GIT_REPO = "myRepo"
            }
            agent {
                docker { 
                    registryUrl 'https://quay.io'
                    registryCredentialsId 'quay-id'
                    image "quay.io/codefresh/codefresh-report-image:0.0.61"
                    args "--net='host'"
                }
            }
            stages {
                stage('report-image') {
                    steps {
                        sh 'node --version'
                        sh 'ls -ltra'
                        sh 'cd /code && yarn start'
                    }
                }
            }
        }
        
    }
}
