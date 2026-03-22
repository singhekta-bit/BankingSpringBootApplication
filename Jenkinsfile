node {
    def tag = "3.0"
    def dockerHubUser = ""
    def containerName = "bankingapp"
    def httpPort = "8989"

    stage('Prepare Environment'){
        echo 'Initialize Environment'
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            dockerHubUser = "${dockerUser}"
        }
    }

    stage('Code Checkout'){
        try {
            checkout scm
        } catch(Exception e) {
            echo "Exception occurred in Git Code Checkout: ${e.message}"
            currentBuild.result = "FAILURE"
            throw e 
        }
    }

    stage('Maven Build') {
        def mavenHome = tool name: 'maven3', type: 'maven'
        sh "${mavenHome}/bin/mvn clean package"
    }

    stage('Docker Image Build'){
        echo 'Creating Docker image'
        sh "docker build -t ${dockerHubUser}/${containerName}:${tag} . --pull --no-cache"
    } 
	
    stage('Publishing Image to DockerHub'){
        echo 'Pushing the docker image to DockerHub'
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh "echo '${dockerPassword}' | docker login -u ${dockerUser} --password-stdin"
            sh "docker push ${dockerHubUser}/${containerName}:${tag}"
            echo "Image push complete"
        } 
    }    

    stage('Ansible Playbook Execution'){
        withCredentials([string(credentialsId: 'ssh_password', variable: 'AZURE_PASS')]) {
            // Using triple double-quotes (""") allows you to use ${variable} syntax directly
            sh "export ANSIBLE_HOST_KEY_CHECKING=False && ansible-playbook -i inventory.yaml containerDeploy.yaml -e httpPort=$httpPort -e containerName=$containerName -e dockerImageTag=$dockerHubUser/$containerName:$tag -e -e ansible_password='${AZURE_PASS}' --become"
    
        }
    }
}
