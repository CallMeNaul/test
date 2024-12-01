pipeline {
    agent {
        label 'naul'
    }
    environment {
        sourceCode = "https://github.com/CallMeNaul/test.git"
        DEPLOYMENT_FILE = 'deployment.yaml'
        BRANCH_NAME = 'test'
        GITHUB_CREDENTIALS = credentials('login-and-push-from-jenkins')
        GITHUB_TOKEN = "${GITHUB_CREDENTIALS.PASSWORD}"
        GITHUB_USERNAME = "${GITHUB_CREDENTIALS.USR}"
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
                    sh 'git config user.email "luanyou952003@gmail.com"' // Thay đổi thành email của bạn
                    sh 'git config user.name "CallMeNaul"' // Thay đổi thành username của bạn
                    
                    def currentBranch = sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()
                    if (currentBranch != "test") {
                        echo "Current branch is ${currentBranch}. Switching to branch 'test'."
                        sh 'git checkout test' // Chuyển sang nhánh test
                    }
                    
                    def remoteUrl = sh(script: "git remote get-url origin", returnStdout: true).trim()
                    if (remoteUrl != "https://github.com/CallMeNaul/test.git") {
                        echo "Remote URL is ${remoteUrl}. Adding the correct remote."
                        sh "git remote remove origin" // Xóa remote cũ (nếu cần thiết)
                        sh "git remote add origin https://github.com/CallMeNaul/test.git" // Thêm remote mới
                    }
                    sh 'git add .'
                    sh 'git commit -m "Update deployment file to use version v${BUILD_NUMBER}"'
                    sh 'git push https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/CallMeNaul/test.git test'
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
