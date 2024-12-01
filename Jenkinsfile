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
                git sourceCode
            }
        }
        stage('Update Deployment File') {
            steps {
                script {
                    def buildNumber = env.BUILD_NUMBER
                    // Đọc nội dung file deployment.yaml
                    def fileContent = readFile("${DEPLOYMENT_FILE}")
                    // Thay thế callmenaul:threaddit-v3:latest bằng callmenaul:threaddit-v{BUILD-NUMBER}:latest
                    def updatedContent = fileContent.replaceAll(/callmenaul:threaddit-v\d+:(latest)/, "callmenaul:threaddit-v${buildNumber}:latest")
                    // Ghi nội dung đã cập nhật vào file
                    writeFile file: "${DEPLOYMENT_FILE}", text: updatedContent
                }
            }
        }
        stage('Commit Changes') {
            steps {
                script {
                    sh 'git config user.email "luanyou952003@gmail.com"' // Thay đổi thành email của bạn
                    sh 'git config user.name "CallMeNaul"' // Thay đổi thành username của bạn
                    sh 'git add .'
                    sh 'git commit -m "Update deployment file to use version v${BUILD_NUMBER}"'
                    sh 'git remote add origin ${sourceCode}'
                    sh 'git push origin ${BRANCH_NAME}' // Thay đổi thành branch bạn muốn push
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
