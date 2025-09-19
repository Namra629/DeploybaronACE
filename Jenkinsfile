pipeline {
    agent any

    environment {
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

        stage('Deploy Bar to ACE') {
            steps {
                sh '''#!/bin/bash
echo "Loading ACE environment"
. ${ACE_PROFILE}   



   # Check if broker exists before creating
                if ! mqsilist | grep -q '2ndnode'; then
                    echo "Creating broker 2ndnode"
                    mqsicreatebroker 2ndnode
                    echo "Sleeping for 5 seconds before starting broker..."
                    sleep 5
                    mqsistart 2ndnode
                else
                    echo "Broker 2ndnode already exists"
                    echo "Restarting broker to be safe..."
                    mqsistop 2ndnode -i
                    echo "Sleeping for 5 seconds before restart..."
                    sleep 5
                    mqsistart 2ndnode
                fi

# Check if execution group exists before creating
if ! mqsilist 2ndnode | grep -q '2ndserver'; then
    echo "Creating execution group 2ndserver"
    mqsicreateexecutiongroup 2ndnode -e 2ndserver
else
    echo "Execution group 2ndserver already exists"
fi

# Deploy BAR
echo "Deploying HelloWorld.bar"
mqsideploy 2ndnode -e 2ndserver -a HelloWorld.bar
'''
            }
        }
    }
}
