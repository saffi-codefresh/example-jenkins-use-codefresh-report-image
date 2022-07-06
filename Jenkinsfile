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
                sh '''
                    IMAGE_NAME="safficodefresh/test-report-image-jenkins:$BUILD_NUMBER"
                    docker pull $IMAGE_NAME
                    '''
            }
        }
        
        stage('call-report') {
            environment {
                CF_ENRICHERS = 'jira'
                CF_BRANCH = 'main'
                CF_HOST = 'https://saffi.pipeline-team.cf-cd.com'
                CF_API_KEY = credentials('CF_API_KEY')
                CF_IMAGE = "safficodefresh/test-report-image-jenkins:${env.BUILD_NUMBER}"
                CF_CONTAINER_REGISTRY_INTEGRATION= 'docker'
                CF_JIRA_INTEGRATION= 'jira'
                CF_JIRA_MESSAGE= '''
                        A message with embedded issue ( i.e. CR-11027 )
                        that would be use query jira for the ticket '''
                CF_JIRA_PROJECT_PREFIX = 'CR'
                CF_WORKFLOW_NAME = "${env.JOB_NAME}"
                CF_WORKFLOW_URL = "${env.JOB_URL}"
            }
            steps {
                sh '''
                    VERSION="0.0.70"
                    EXTERNAL_ENV=$(jq -n 'env'|base64)
                    echo "EXTERNAL_ENV=$EXTERNAL_ENV">cf_env
                    
                    docker run --env-file=cf_env "quay.io/codefresh/codefresh-report-image:$VERSION"                   
                '''
            }
//             env>cf_env
//                     cat cf_env
//                     EXTERNAL_ENV=$(jq -n 'env'|base64)
//                     echo "EXTERNAL_ENV=$EXTERNAL_ENV">cf_env
//                     echo>cf_env
//                     for var in $(compgen -e); do
//                       Q='"'
//                       if [[ $var == CF_* ]]
//                       then
//                       echo "$var=$Q${!var}$Q">>cf_env
//                       fi
//                     done            
//             agent {
//                 docker { 
//                     registryUrl 'https://quay.io'
//                      registryCredentialsId 'quay-id'
//                     image "quay.io/codefresh/codefresh-report-image:0.0.62"
//                 }
//             }
//             steps {
//                 sh 'node --version'
//                 sh 'cd /code && yarn start'
//             }
        }
        
    }
}
