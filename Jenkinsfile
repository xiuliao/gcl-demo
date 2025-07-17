pipeline {
    agent any
    
    stages {
        stage('Setup and Checkout') {
            steps {
                script {
                    // 在Jenkins工作空间中检出代码
                    checkout scm
                    echo "✅ 代码检出完成"

                    // 显示当前分支和提交信息
                    def gitBranch = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
                    def gitCommit = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
                    echo "🌿 当前分支: ${gitBranch}"
                    echo "📝 提交哈希: ${gitCommit}"

                    // 显示当前工作目录
                    def currentDir = pwd()
                    echo "📁 当前工作目录: ${currentDir}"
                }
            }
        }
    
        
        stage('List files') {
            steps {
                script {
                    def workspace = pwd()
                    echo "📁 当前工作目录: ${workspace}"
                    sh 'ls -la'
                }
            }
        }

        stage('Execute GCL Files') {
            steps {
                script {
                    // 获取当前Jenkins工作空间路径
                    def jenkinsWorkspace = pwd()
                    echo "📁 Jenkins工作空间: ${jenkinsWorkspace}"

                    // 构建GCL文件的完整路径
                    def gclFile = "${jenkinsWorkspace}/Ballot.gcl"
                    echo "🚀 执行GCL文件: ${gclFile}"

                    // 切换到/opt/GCL/bin目录执行chsimu命令
                    dir('/opt/GCL/bin') {
                        def currentDir = pwd()
                        echo "📁 当前执行目录: ${currentDir}"

                        try {
                            def result = sh(
                                script: "chsimu \"${gclFile}\" -stdout",
                                returnStdout: true
                            )
                            echo "✅ 执行结果:"
                            echo result
                        } catch (Exception e) {
                            echo "❌ 执行失败: ${e.getMessage()}"
                        }
                    }
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    echo "🧹 清理workspace目录..."
                    sh 'rm -rf /var/jenkins_home/workspace/test'
                    echo "✅ 清理完成"
                }
            }
        }

    }
    
    post {
        always {
            echo "🧹 清理工作空间..."
            
            // 清理构建产物
            script {
                if (fileExists('node_modules')) {
                    sh 'rm -rf node_modules'
                }
                if (fileExists('target')) {
                    sh 'rm -rf target'
                }
            }
            
            // 清理工作空间
            cleanWs()
            echo "✅ 清理完成"
        }
        
        success {
            echo "🎉 流水线执行成功！"
            
            script {
                def duration = currentBuild.durationString
                def buildNumber = env.BUILD_NUMBER
                def jobName = env.JOB_NAME
                
                echo "📊 构建统计:"
                echo "   - 构建编号: ${buildNumber}"
                echo "   - 任务名称: ${jobName}"
                echo "   - 构建时长: ${duration}"
            }
        }
        
        failure {
            echo "❌ 流水线执行失败！"
            
            script {
                def buildNumber = env.BUILD_NUMBER
                def jobName = env.JOB_NAME
                def buildUrl = env.BUILD_URL
                
                echo "💥 失败信息:"
                echo "   - 构建编号: ${buildNumber}"
                echo "   - 任务名称: ${jobName}"
                echo "   - 构建链接: ${buildUrl}"
                echo "   - 请检查构建日志以获取详细错误信息"
            }
        }
        
        unstable {
            echo "⚠️ 流水线执行不稳定"
            echo "   - 可能存在测试失败或其他警告"
            echo "   - 请检查构建日志"
        }
    }
}
