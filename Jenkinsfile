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

                # Ensure node exists
                if ! mqsilist | grep -q "${NODE_NAME}"; then
                    echo "Creating integration node ${NODE_NAME}..."
                    mqsicreatebroker ${NODE_NAME}
                else
                    echo "Node ${NODE_NAME} already exists"
                fi

                # Start node if not running
                NODE_STATUS=$(mqsilist | grep "${NODE_NAME}" | awk '{print $2}' | head -n1)
                if [ "$NODE_STATUS" != "Running" ]; then
                    echo "Starting integration node ${NODE_NAME}..."
                    mqsistart ${NODE_NAME}

                    # Wait until node is running
                    while true; do
                        NODE_STATUS=$(mqsilist | grep "${NODE_NAME}" | awk '{print $2}' | head -n1)
                        if [ "$NODE_STATUS" == "Running" ]; then
                            echo "Node ${NODE_NAME} is now running."
                            break
                        else
                            echo "Waiting for node to start..."
                            sleep 5
                        fi
                    done
                else
                    echo "Node ${NODE_NAME} is already running."
                fi

                # Ensure server exists
                if ! mqsilist ${NODE_NAME} | grep -q "${SERVER_NAME}"; then
                    echo "Creating integration server ${SERVER_NAME}..."
                    mqsicreateexecutiongroup ${NODE_NAME} -e ${SERVER_NAME}
                else
                    echo "Server ${SERVER_NAME} already exists"
                fi

                # Optional sleep before deployment
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
