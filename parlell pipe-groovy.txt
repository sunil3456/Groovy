def performDeploymentStages(String node, String app) {
    stage("build") {
        echo "Building the app [${app}] on node [${node}]"
    }
    stage("deploy") {
        echo "Deploying the app ${app}] on node [${node}]"
    }
    stage("test") {
        echo "Testing the app [${app}] on node [${node}]"
    }
}

pipeline {
    agent {
        label 'master'
    }
    parameters {
        string(name: 'NODES', defaultValue: '1,2,3', description: 'Nodes to build, deploy and test')
        choice(name: 'ENV', choices: 'qa', description: 'Environment')
        string(name: 'APPS', defaultValue: 'app01,app02', description: 'App names')
    }

    stages {
        stage('parallel stage') {
            steps {
                script {
                    def nodes = [:]
                    for (node in params.NODES.tokenize(',')) {
                        def apps = [:]
                        for (app in params.APPS.tokenize(',')) {
                            performDeploymentStages(node, app)
                        }
                        parallel apps
                    }
                    parallel nodes
                }
            }
        }
    }
}