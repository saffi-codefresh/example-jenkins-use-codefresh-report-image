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
        
        stage('report image') {
            environment {
                CF_ENRICHERS = 'jira git'
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
                CF_WORKFLOW_URL = "${env.BUILD_URL}"
                CF_GITHUB_TOKEN = credentials('CF_GITHUB_TOKEN')
                // CF_GITHUB_API_HOST_URL = "https://api.github.com" // Thats the default github service api
                // CF_GITHUB_API_PATH_PREFIX = "" // default empty - no prefix
                CF_CI_TYPE = "jenkins"
                CF_GIT_REPO = "saffi-codefresh/example-jenkins-use-codefresh-report-image"
                CF_GIT_PROVIDER = "github"
            }
            agent {
                docker { 
                    registryUrl 'https://quay.io'
                    registryCredentialsId 'quay-id'
                    image "quay.io/codefresh/codefresh-report-image:0.0.80"
                }
            }
            steps {
                
                sh '''
                    # add git branch
                    CF_BRANCH="${GIT_BRANCH#*/}"
                    
                    echo $(env)
                    node --version
                    cd /code && yarn start'''
            }
// Alternative implementation            
//             steps {
//                 sh '''
//                     # add git branch            
//                     export CF_BRANCH="${GIT_BRANCH#*/}"
//                     env | cut -f 1 -d "=" | grep -E "^CF_"  > cf_env
//                     echo "using $(cat cd_env|xargs echo)"
//                     # docker run --env-file=cf_env "quay.io/codefresh/codefresh-report-image:$VERSION"
//                     VERSION="a0.0.3"
//                 docker run --env-file=cf_env "safficodefresh/codefresh-report-image:$VERSION"    


                   
//                 '''
//             }
//              VERSION="0.0.80"
//                     KEYS=($(jq -n 'env' -S -M -c | jq 'keys' -M -c))
//                     arr=()
//                     for i in $(echo $KEYS | tr "[" "\n" | tr "]" "\n" | tr '"' '\n' | tr "," "\n")
//                     do
//                       if [[ $i == CF_* ]]
//                       then 
//                         arr+=" -e $i "
//                       fi                    
//                     done
//                     # echo "$arr"
                    
//                     # docker run $arr "quay.io/codefresh/codefresh-report-image:$VERSION"  
//                     docker run $arr saffi-codefresh/codefresh-report-image:a0.0.4
        }
    }
}
