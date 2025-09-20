pipeline {
    agent any

    environment { 
        ACE_PROFILE = '/home/Namra/ace-12.0.12.16/server/bin/mqsiprofile' 
        NODE_NAME   = 'testnode'
        SERVER_NAME = 'helloworld'
        BAR_FILE    = 'HelloWorld.bar'
    }

    stages { 
        stage('Git Checkout') { 
            steps { 
                git branch: 'main', 
                    url: 'https://github.com/Namra629/DeploybaronACE', 
                    credentialsId: 'git_cred' 
            } 
        } 

        stage('Setup and Deploy') {
            steps {
                sh '''#!/bin/bash
                set -e
                echo "Loading ACE environment"
                . ${ACE_PROFILE}

                echo "Starting integration node ${NODE_NAME}..."
                mqsistart ${NODE_NAME} -i
                sleep 5

                echo "Deploying ${BAR_FILE} to ${NODE_NAME}/${SERVER_NAME}..."
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}
                '''
            }
        }
    }
}
