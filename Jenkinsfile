piepline{
    agnet any
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
        stage('setup and deploy'){
            sh ''' #!/bin/bash
            mqsistart testnode
            sleep 5
            mqsideploy testnode -e helloworld -a HelloWorld.bar
            '''
        }
        }
}
