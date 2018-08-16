pipeline {
  agent {
    kubernetes {
      //cloud 'kubernetes'
      label 'kaniko'
      yaml """
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
"""
      }
    }
  stages {
    stage('Build with Kaniko') {
      environment {
        PATH = "/busybox:$PATH"
      }
      steps {
        git 'https://github.com/jenkinsci/docker-jnlp-slave.git'
        container(name: 'kaniko', shell: '/busybox/sh') {
            sh '''#!/busybox/sh
            /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure-skip-tls-verify --destination=index.docker.io/andperu/test66
            '''
        }
      }
    }
  }
}