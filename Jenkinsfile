pipeline {
    agent {
        label 'naul'
    }
    environment {
        sourceCode = "https://github.com/CallMeNaul/test.git"
        DEPLOYMENT_FILE = 'deployment.yaml'
        BRANCH_NAME = 'test'
        GITHUB_CREDENTIALS = credentials('login-and-push-from-jenkins')
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
        stage('Setup Git Configuration') {
            steps {
                script {
                    def email = "luanyou952003@gmail.com" // Thay đổi thành email của bạn
                    def username = "CallMeNaul" // Thay đổi thành username của bạn
                    def configuredEmail = sh(script: "git config --get user.email", returnStdout: true).trim()
                    if (configuredEmail != email) {
                        echo "Configuring user.email to ${email}"
                        sh "git config user.email '${email}'"
                    }
                    
                    def configuredUsername = sh(script: "git config --get user.name", returnStdout: true).trim()
                    if (configuredUsername != username) {
                        echo "Configuring user.name to ${username}"
                        sh "git config user.name '${username}'"
                    }

                    def remoteUrl = sh(script: "git remote get-url origin", returnStdout: true).trim()
                    if (remoteUrl != "https://github.com/CallMeNaul/test.git") {
                        echo "Remote URL is ${remoteUrl}. Adding the correct remote."
                        sh "git remote remove origin" // Xóa remote cũ (nếu cần thiết)
                        sh "git remote add origin https://github.com/CallMeNaul/test.git" // Thêm remote mới
                    }
                    def currentBranch = sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()
                    if (currentBranch != "test") {
                        echo "Current branch is ${currentBranch}. Switching to branch 'test'."
                        sh 'git checkout test' // Chuyển sang nhánh test
                    }
                }
            }
        }
        stage('Update Deployment File') {
            steps {
                script {
                    sh 'cat ${DEPLOYMENT_FILE}'
                    def buildNumber = env.BUILD_NUMBER
                    // Thay thế callmenaul:threaddit-v3:latest bằng callmenaul:threaddit-v{BUILD-NUMBER}:latest
                    sh 'sed -i "s/callmenaul\\/threaddit-v[^:]*:latest/callmenaul\\/threaddit-v${BUILD_NUMBER}:latest/g" ${DEPLOYMENT_FILE}'
                    sh 'cat ${DEPLOYMENT_FILE}'
                }
            }
        }
        stage('Commit Changes') {
            steps {
                script {
                    sh 'git add .'
                    sh 'git commit -m "Update deployment file to use version v${BUILD_NUMBER}"'
                    withCredentials([usernamePassword(credentialsId: 'login-and-push-from-jenkins', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')]) {
                        sh 'git push https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/CallMeNaul/test.git test'}
                    
                    //sh 'git push origin ${BRANCH_NAME}' // Thay đổi thành branch bạn muốn push
                }
            }
        }
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
