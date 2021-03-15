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
    stage ('prepare') {
      steps {
        withCredentials([file(credentialsId: 'ack-01', variable: 'KUBECONFIG')]) {
          sh "mkdir -p ${env.WORKSPACE}/.kube && cp ${KUBECONFIG} ${env.WORKSPACE}/.kube/config"
          sh "cat ${env.WORKSPACE}/.kube/config"
        }
      }
    }
    stage ('build & push') {
        steps {
            container ('mvn') {
                sh 'mvn -o -Dmaven.test.skip=true -gs `pwd`/configuration/settings.xml clean package'
                sh 'docker build -f Dockerfile -t $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER .'
                sh 'docker login $REGISTRY -u ${DOCKER_USERNAME}'
                sh 'docker push $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER'
            
            }
        }
    }
    stage ('deploy to dev') {
      when {
        environment name: 'RUN_ENV',
        value: 'dev'
      }
      steps {
        kubernetesDeploy(configs: 'deploy/dev/**', enableConfigSubstitution: true, kubeConfig: [path: '.kube/config'])
      }
    }
    stage ('deploy to test') {
      when {
        environment name: 'RUN_ENV',
        value: 'test'
      }
      steps {
        kubernetesDeploy(configs: 'deploy/test/**', enableConfigSubstitution: true, kubeConfig: [path: '.kube/config'])
      }
    }
  }
}
