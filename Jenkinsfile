pipeline {
    agent any
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
                sh """
                echo "Loading MQ/ACE environment"
                source /home/Namra/ace-12.0.12.16/server/bin/mqsiprofile

                # Check if broker exists before creating
                if ! mqsilist | grep -q '2ndnode'; then
                    echo "Creating broker 2ndnode"
                    mqsicreatebroker 2ndnode
                    mqsistart 2ndnode
                else
                    echo "Broker 2ndnode already exists"
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
                """
            }
        }
    }
}
