pipeline {
  agent any
  stages {
    stage('Check Environment') {
      steps {
        withKubeConfig(contextName: 'c2.fra.k8scluster.de', credentialsId: '24d2e3c8-8b53-4333-99d4-62181446e589') {
          sh 'kubectl version'
        }

      }
    }
  }
}