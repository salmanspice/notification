pipeline {
     agent { 
                label 'linux'
            }
	//   parameters {
	//   choice choices: ['US','UK','DE','ES','FR','IT','NL'], description: "Choose to which Country Build Deploy .", name: "DEPLOY_BUILD_TO_COUNTRY"
	//   }
//   parameters {
//         string(name: 'BUILD_NAME', defaultValue: "${env.BUILD_NUMBER}", description: 'Enter the build name')
//     }
environment {

     GIT_COMMIT_LIST = sh(script: 'git log $(git describe --tags --abbrev=0) HEAD --oneline', returnStdout: true).trim()
     Author_Name = sh(script: "git show -s --pretty=%ae", returnStdout: true).trim()
     
  }
stages {
    stage('git clone') {
      
       steps {
           
           git branch: 'main', url: 'https://github.com/salmanspice/notification.git'
           
        }
    }
    // stage('Buil the code'){
      
    //     steps{
    //         echo 'Building the code.....'
    //           script {
    //                 currentBuild.displayName = params.BUILD_NAME+'#'+currentBuild.number
    //             }
	
    //     }
    // }
    stage('Creating Artifact'){
        steps{
            archiveArtifacts artifacts: '**', followSymlinks: false
        }
    }
	stage('Get User') {
            steps {
                script {
                    def buildCause = currentBuild.getBuildCauses()[0]
                    def userId = buildCause.userId
                    def userName = buildCause.shortDescription
                    echo "User ID: ${userId}"
                    echo "User Name: ${userName}"
                }
            }
        }

    stage('deploy the code on server'){
        
        steps{
           sshPublisher(publishers: [sshPublisherDesc(configName: 'salman', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/var/www-salman/rollback-demo-salman-khan', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
        }
    }
}
post {
        success {
            
           
                office365ConnectorSend webhookUrl: 'https://syncoms.webhook.office.com/webhookb2/842b4711-48e1-4480-898f-35e053fb0f4f@49099e21-d9d9-45dc-b95b-f5f4afb3b3ff/JenkinsCI/ccc82cd3928c44879188b96bbca17c84/27298b69-d954-44d3-b95b-6ff3939a423e',
                message: """
                
                    Job ${env.JOB_NAME} (${env.BUILD_NUMBER}) has completed successfully!

                    Git Commit: "$GIT_COMMIT"
                    Git Branch: "$GIT_BRANCH"
                    Build URL: "$BUILD_URL"
		    Build User: "$BUILD_USER"
		    Git Commit List: "$GIT_COMMIT_LIST"
                    Auother Name: "$Author_Name"
		
                    Web Link: "$GIT_URL/commit/$GIT_COMMIT
                    Jenkins URL: "$JENKINS_URL"
                    Build TAG: "$BUILD_TAG"
                """,
                status: 'Success'
            //)
        }
    }
}
