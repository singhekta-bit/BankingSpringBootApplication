node{
    def tag, dockerHubUser, containerName, httpPort = ""
    stage('Prepare Environment'){
        echo 'Initialize Environment'
        tag="3.0"
	withCredentials([usernamePassword(credentialsId: 'ekta13singh', usernameVariable: 'ekta13singh', passwordVariable: '$impli@#le321')]) {
		dockerHubUser="$dockerUser"
        }
	containerName="bankingapp"
	httpPort="8989"
    }
    stage('Code Checkout'){
        try{
            checkout scm
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
        }
    }
}

stage('Maven Build'){
        sh "mvn clean package"        
    }

