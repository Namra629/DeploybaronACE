pipeline{
    agent any
    stages{
        stage('Git Checkout'){
            steps{
                git branch: 'main',
                    url: 'https://github.com/Namra629/DeploybaronACE',
                    credentialsId: 'git_cred'
            }

        }
        stage('Deploy Bar to ACE'){
           steps{
            sh ''' #!/bin/bash
            echo "Loading MQ environment"
        
            . /home/Namra/ace-12.0.12.16/server/bin/mqsiprofile 
            
            mqsicreatebroker 2ndnode
            mqsistart 2ndnode
            mqsicreateexecutiongroup 2ndnode -e 2ndserver
            mqsideploy 2ndnode -e 2ndserver -a HelloWorld.bar

            '''

              
           }
        }
    }
}
