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

                # Create node if it doesn't exist
                if ! mqsilist | grep -q "${NODE_NAME}"; then
                    echo "Creating integration node ${NODE_NAME}..."
                    mqsicreatebroker ${NODE_NAME}
                else
                    echo "Node ${NODE_NAME} already exists"
                fi

                # Create server if it doesn't exist
                if ! mqsilist ${NODE_NAME} | grep -q "${SERVER_NAME}"; then
                    echo "Creating integration server ${SERVER_NAME}..."
                    mqsicreateexecutiongroup ${NODE_NAME} -e ${SERVER_NAME}
                else
                    echo "Server ${SERVER_NAME} already exists"
                fi

                # Start node only if not already running
                NODE_STATUS=$(mqsilist | grep "${NODE_NAME}" | awk '{print $2}')
                if [ "$NODE_STATUS" != "Running" ]; then
                    echo "Starting integration node ${NODE_NAME}..."
                    mqsistart ${NODE_NAME}
                    echo "Waiting for 10 seconds to ensure node is fully started..."
                    sleep 10
                else
                    echo "Node ${NODE_NAME} is already running"
                fi

                # Optional sleep before deployment to ensure server readiness
                echo "Waiting 5 seconds before deploying BAR..."
                sleep 5

                # Deploy BAR
                echo "Deploying ${BAR_FILE} to ${NODE_NAME}/${SERVER_NAME}..."
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}

                echo "Deployment completed successfully!"
                '''
            }
        }
    }
}

