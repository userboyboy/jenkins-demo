pipeline {
  agent {
    kubernetes {
      cloud 'kubernetes-ACK'
      label 'mvn'
    }
  }
  parameters {
    choice(
          name: 'RUN_ENV',
          choices: ['dev', 'test'],
          description: '请选择发布环境',
    )
  }
  environment {
      KUBECONFIG_CREDENTIAL_ID = 'ack-01'
      REGISTRY = 'registry.cn-shanghai.aliyuncs.com'
      HARBOR_NAMESPACE = 'sino-test01'
      APP_NAME = 'mvn'
  }
  stages {
        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'test-k8s-haha01', contextName: '', credentialsId: 'ack-01', namespace: 'sino-ops', serverUrl: '') {
                    sh "mkdir -p /.kube && cp ${ack-01} /.kube/config"
                    sh "cat /.kube/config"
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
