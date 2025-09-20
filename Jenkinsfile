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
 
                echo "Starting integration node "
                mqsistart testnode
                sleep 5
 
                echo "Deploying bar"
                mqsideploy testnode -e helloworld -a HelloWorld.bar
 
                echo "Deployment completed successfully!"
                '''
            }
        }
    }
}
