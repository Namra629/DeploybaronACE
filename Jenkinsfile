pipeline {
    agent any
    environment {
        ACE_PROFILE = '/home/Namra/ace-12.0.12.16/server/bin/mqsiprofile'
        NODE_NAME   = 'testnode'
        SERVER_NAME = 'helloworld'
        BAR_FILE    = 'HelloWorld.bar'
        STDOUT_FILE = '/var/mqsi/components/testnode/stdout'
        STDERR_FILE = '/var/mqsi/components/testnode/stderr'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Namra629/DeploybaronACE',
                    credentialsId: 'git_cred'
            }
        }

        stage('Start Node Detached') {
            steps {
                sh '''#!/bin/bash
                set -e

                echo "Starting integration node ${NODE_NAME} in detached mode..."
                nohup bash -lc ". ${ACE_PROFILE}; mqsistart ${NODE_NAME}" \
                    > ${STDOUT_FILE} 2> ${STDERR_FILE} & disown

                echo "Sleeping 10 seconds to allow node to fully start..."
                sleep 10
                '''
            }
        }

        stage('Deploy BAR') {
            steps {
                sh '''#!/bin/bash
                set -e

                echo "Deploying ${BAR_FILE} to ${NODE_NAME}/${SERVER_NAME}..."
                bash -lc ". ${ACE_PROFILE}; mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}"

                echo "Deployment completed successfully!"
                '''
            }
        }
    }
}



