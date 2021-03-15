pipeline {
    // 定义groovy脚本中使用的环境变量。
    environment {
        // 本示例中使用DEPLOY_TO_K8S变量来决定把应用部署到哪套容器集群环境中，如“Production Environment”， “Staging001 Environment”等。
        IMAGE_TAG = sh(returnStdout: true, script: 'echo $image_tag').trim()
        ORIGIN_REPO = sh(returnStdout: true, script: 'echo $origin_repo').trim()
        REPO = sh(returnStdout: true, script: 'echo $repo').trim()
        BRANCH = sh(returnStdout: true, script: 'echo $branch').trim()
    }
    agent {
		kubernetes {
		  cloud 'kubernetes-ACK'
		  label 'mvn'
		}
    }
    stages {
		stage ('prepare') {
		  steps {
			withCredentials([file(credentialsId: 'config', variable: 'KUBECONFIG')]) {
			  sh "mkdir -p ${env.WORKSPACE}/.kube && cp ${KUBECONFIG} ${env.WORKSPACE}/.kube/config"
			}
		  }
		}
        stage('Package') {
            steps {
                container("mvn") {
                    sh "ls"
                    sh "mvn package -B -DskipTests"
                }
            }
        }
        stage('Info') {
            steps {
                container("mvn") {
                    sh "ls"
                    sh "hostname"
                    sh "mvn -v"
                }
            }
        }
    }
}
