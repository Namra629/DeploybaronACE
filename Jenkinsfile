pipeline {
    agent any

    environment {
        ACE_PROFILE = '/home/Namra/ace-12.0.12.16/server/bin/mqsiprofile'
        NODE_NAME   = 'testnode'
        SERVER_NAME = '2ndserver'
        BAR_FILE    = 'HelloWorld.bar'
    }

    stages {
        stage('Setup and Deploy') {
            steps {
                sh '''#!/bin/bash
                set -e

                echo "Loading ACE environment"
                source ${ACE_PROFILE}

                # Create node if not exists
                if ! mqsilist | grep -q "${NODE_NAME}"; then
                    echo "Creating integration node ${NODE_NAME}"
                    mqsicreatebroker ${NODE_NAME}
                else
                    echo "Integration node ${NODE_NAME} already exists"
                fi

                # Start node
                echo "Starting integration node ${NODE_NAME}"
                mqsistart ${NODE_NAME}
                echo "Waiting 5 seconds for node to fully start..."
                sleep 5

                # Create server if not exists
                if ! mqsilist ${NODE_NAME} | grep -q "${SERVER_NAME}"; then
                    echo "Creating integration server ${SERVER_NAME}"
                    mqsicreateexecutiongroup ${NODE_NAME} -e ${SERVER_NAME}
                else
                    echo "Integration server ${SERVER_NAME} already exists"
                fi

                # Deploy BAR file
                echo "Deploying ${BAR_FILE} to ${NODE_NAME}/${SERVER_NAME}"
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}

                echo " Deployment complete"
                mqsilist
                '''
            }
        }
    }
}
