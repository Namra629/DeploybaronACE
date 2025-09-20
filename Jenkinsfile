pipeline{
    agent any
    stages{
        stage('Git Checkout'){
            steps{
                git branch: 'main',
                    url: 'https://github.com/Namra629/DeploybaronACE',
                    credentialsId: 'git_cred'
            }

        }
        stage('Deploy Bar to ACE'){
           steps{
            set -e
                echo "Loading ACE environment"
                . ${ACE_PROFILE}  # source in current shell context

                echo "Starting integration node ${NODE_NAME}..."
                mqsistart ${NODE_NAME}
                sleep 5

               

                echo "Deploying ${BAR_FILE}..."
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}

                echo "Deployment completed successfully!"
                '''
         

              
           }
        }
    }
}
