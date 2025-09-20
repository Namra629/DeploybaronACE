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

                if  mqsilist | grep -q 'testnode'; then 

                    echo "Stopping broker testnode" 

                    mqsistop testnode 

                    echo "Sleeping for 5 seconds before starting broker..." 

                    sleep 5 
                    mqsistart testnode

                else 

                    echo "Broker testnode doesnot exist"  

     

                fi 

  

# Check if execution group exists before creating 

if ! mqsilist testnode | grep -q '2ndserver'; then 

    echo "Creating execution group 2ndserver" 

    mqsicreateexecutiongroup testnode -e 2ndserver 

else 

    echo "Execution group 2ndserver already exists" 

fi 

  

# Deploy BAR 

echo "Deploying HelloWorld.bar" 

mqsideploy testnode -e 2ndserver -a HelloWorld.bar 

''' 

            } 

        } 

    } 

} 
