pipeline {
    agent { label 'slave' }

    stages {
        stage('Checkout') {
            steps {
                echo 'Load code from Gitlab'
                git branch: "main",
                    credentialsId: 'git' ,
                    url: 'git@gitlab.com:misterigriks/danit.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Build artifacts'
                sh """
                   mkdir -p pipeline
                   echo \$(date) > pipeline/\${BUILD_NUMBER}.txt
                """
            }
        }
        stage('Publish') {
            steps {
                echo 'Publish'
        	withCredentials([sshUserPrivateKey(credentialsId: 'git', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        git add .
                        git commit -m "Publishing artifacts"
                        git tag v0.2.${BUILD_NUMBER}
                        GIT_SSH_COMMAND="ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -i \${SSH_KEY}" git push origin main --tags
                    """
		}
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: "pipeline/*.txt", onlyIfSuccessful: true
        }
    }
}
