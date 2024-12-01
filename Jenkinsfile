pipeline {
    agent {
        label 'naul'
    }
    environment {
        sourceCode = "https://github.com/CallMeNaul/test.git"
        DEPLOYMENT_FILE = 'deployment.yaml'
        BRANCH_NAME = 'test'
    }
    stages {
        stage('Info') {
            steps {
                sh (script:"""whoami;pwd;ls""", label: "Check information")
            }
        }
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout') {
            steps {
                git branch: "main", url: "${sourceCode}"
            }
        }
        stage('Update Deployment File') {
            steps {
                script {
                    def buildNumber = env.BUILD_NUMBER
                    // Thay thế callmenaul:threaddit-v3:latest bằng callmenaul:threaddit-v{BUILD-NUMBER}:latest
                    sh "sed -i 's/callmenaul:threaddit-v[0-9]*:latest/callmenaul:threaddit-v${buildNumber}:latest/g' ${DEPLOYMENT_FILE}"
                    sh 'cat ${DEPLOYMENT_FILE}'
                }
            }
        }
        // stage('Commit Changes') {
        //     steps {
        //         script {
        //             sh 'git config user.email "luanyou952003@gmail.com"' // Thay đổi thành email của bạn
        //             sh 'git config user.name "CallMeNaul"' // Thay đổi thành username của bạn
        //             sh 'git add .'
        //             sh 'git commit -m "Update deployment file to use version v${BUILD_NUMBER}"'
        //             sh 'git push origin ${BRANCH_NAME}' // Thay đổi thành branch bạn muốn push
        //         }
        //     }
        // }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
