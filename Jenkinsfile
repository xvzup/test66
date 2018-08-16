pipeline {
  agent {
    kubernetes {
      label 'kaniko'
      yaml '''
kind: Pod
metadata:
  name: kaniko
spec:
  template:
    spec:
      initContainers:
      - name: copycontext
        image: busybox
        command: ["sh", "-c"]
        args: ["cp /input/* /context; chmod +x /context/*.py"]
        volumeMounts:
          - name: kaniko-context
            mountPath: /context
          - name: context-input
            mountPath: /input
      containers:
      - name: kaniko
        image: gcr.io/kaniko-project/executor:latest
        args: ["--dockerfile=/context/Dockerfile",
               "--context=dir://context",
               "--destination=index.docker.io/andperu/hello_world",
               "-v=debug"]
        volumeMounts:
          - name: kaniko-secret
            mountPath: .docker/
          - name: kaniko-context
            mountPath: /context
      restartPolicy: Never
      volumes:
        - name: kaniko-secret
          secret:
            secretName: kaniko-secret
        - name: kaniko-context
          persistentVolumeClaim:
            claimName: kaniko
        - name: context-input
          configMap:
            name: context-input
'''
    }

  }
  stages {
    stage('Build with Kaniko') {
      steps {
        withKubeConfig(contextName: 'c2.fra.k8scluster.de', credentialsId: '24d2e3c8-8b53-4333-99d4-62181446e589') {
          sh 'kubectl version'
        }

      }
    }
  }
}