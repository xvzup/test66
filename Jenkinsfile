pipeline {
  agent any
  stages {
    stage('Check Environment') {
      steps {
        withKubeConfig(contextName: 'c2.fra.k8scluster.de', credentialsId: '24d2e3c8-8b53-4333-99d4-62181446e589') {
          sh '''echo "Checking for kaniko-secret"

if [ `kubectl get secret | grep kaniko-secret | wc -l`-ne 1 ]; then
  echo "kaniko-secret does not exist"
  exit -1
fi





'''
        }

      }
    }
  }
}