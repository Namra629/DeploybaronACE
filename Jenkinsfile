pipeline {
    agent any

    environment { 
        NODE_NAME   = 'testnode'
        SERVER_NAME = 'helloworld'
        BAR_FILE    = 'HelloWorld.bar'
        ACE_PROFILE = '/home/Namra/ace-12.0.12.16/server/bin/mqsiprofile'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Namra629/DeploybaronACE',
                    credentialsId: 'git_cred'
            }
        }

        stage('Start Node, Server, and Deploy BAR') {
            steps {
                sh '''
                # Run all ACE commands in one clean login shell
                bash -lc "
                set -e
                echo 'Loading ACE environment'
                source ${ACE_PROFILE}

                echo 'Starting integration node ${NODE_NAME}...'
                mqsistart ${NODE_NAME}
                echo 'Sleeping 10 seconds to allow node to start...'
                sleep 10

                echo 'Starting server ${SERVER_NAME}...'
                mqsistartmsgflow ${NODE_NAME} -e ${SERVER_NAME}
                sleep 5

                echo 'Deploying ${BAR_FILE} to ${NODE_NAME}/${SERVER_NAME}...'
                mqsideploy ${NODE_NAME} -e ${SERVER_NAME} -a ${BAR_FILE}

                echo 'Deployment completed successfully!'
                "
                '''
            }
        }
    }
}
