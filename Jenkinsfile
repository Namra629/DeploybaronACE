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

        stage('Start Node and Deploy BAR') {
            steps {
                sh '''#!/bin/bash
                set -e

                # Force a login shell to load mqsiprofile properly
                bash -lc '
                echo "Loading ACE environment"
                . ${ACE_PROFILE}

                # Start node detached if not already running
                if ! mqsilist | grep -q "${NODE_NAME}.*running"; then
                    echo "Starting node ${NODE_NAME} detached..."
                    nohup mqsistart ${NODE_NAME} > /var/mqsi/components/${NODE_NAME}/stdout 2> /var/mqsi/components/${NODE_NAME}/stderr &
                    disown
                    sleep 5
                else
                    echo "Node ${NODE_NAME} already running."
                fi

                # Deploy BAR
                echo "Deploying BAR ${BAR_FILE}..."
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}

                echo "Deployment completed successfully!"
                '
                '''
            }
        }
    }
}


