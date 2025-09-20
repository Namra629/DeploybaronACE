pipeline {
    agent any

    environment {
        ACE_PROFILE = '/home/Namra/ace-12.0.12.16/server/bin/mqsiprofile'
        NODE_NAME   = 'testnode'
        SERVER_NAME = '2ndserver'
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

        stage('Deploy BAR to ACE') {
            steps {
                sh '''#!/bin/bash
                set -e

                echo "Loading ACE environment"
                source ${ACE_PROFILE}

                echo "Checking broker ${NODE_NAME}..."
                if ! mqsilist | grep -q "${NODE_NAME}"; then
                    echo "ERROR: Broker ${NODE_NAME} does not exist"
                    exit 1
                fi

                # Start broker if not running
                if ! mqsilist | grep -q "${NODE_NAME}.*running"; then
                    echo "Starting broker ${NODE_NAME}..."
                    mqsistart ${NODE_NAME}

                    # Wait until running
                    for i in {1..30}; do
                        if mqsilist | grep -q "${NODE_NAME}.*running"; then
                            echo "Broker ${NODE_NAME} is running."
                            break
                        fi
                        echo "Waiting for broker to start... ($i)"
                        sleep 5
                    done

                    if ! mqsilist | grep -q "${NODE_NAME}.*running"; then
                        echo "ERROR: Broker ${NODE_NAME} failed to start."
                        exit 1
                    fi
                else
                    echo "Broker ${NODE_NAME} already running."
                fi

                # Check if execution group exists
                if ! mqsilist ${NODE_NAME} | grep -q "${SERVER_NAME}"; then
                    echo "Creating execution group ${SERVER_NAME}"
                    mqsicreateexecutiongroup ${NODE_NAME} -e ${SERVER_NAME}
                else
                    echo "Execution group ${SERVER_NAME} already exists"
                fi

                # Deploy BAR file
                echo "Deploying ${BAR_FILE}..."
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}
                '''
            }
        }
    }
}
