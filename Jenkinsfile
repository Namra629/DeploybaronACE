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

        stage('Start Node and Server') {
            steps {
                sh '''#!/bin/bash
                set -e

                echo "Loading ACE environment"
                . ${ACE_PROFILE}

                # Start the node if not running
                if ! mqsilist | grep -q "${NODE_NAME}" | grep -qi "Running"; then
                    echo "Starting integration node ${NODE_NAME}..."
                    mqsistart ${NODE_NAME}
                    echo "Waiting 5 seconds for node to be fully started..."
                    sleep 5
                else
                    echo "Node ${NODE_NAME} is already running."
                fi

                # Start the server if not running
                SERVER_STATUS=$(mqsilist ${NODE_NAME} | grep "${SERVER_NAME}" | awk '{print $3}' | head -n1)
                if [ "$SERVER_STATUS" != "Running" ]; then
                    echo "Starting server ${SERVER_NAME} on node ${NODE_NAME}..."
                    mqsistartmsgflow ${NODE_NAME} -e ${SERVER_NAME}
                    # Some ACE versions require starting the execution group explicitly
                    echo "Waiting 5 seconds for server to be fully started..."
                    sleep 5
                else
                    echo "Server ${SERVER_NAME} is already running."
                fi

                # Deploy BAR
                echo "Deploying ${BAR_FILE} to ${NODE_NAME}/${SERVER_NAME}..."
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}

                echo "Deployment completed successfully!"
                '''
            }
        }
    }
}

