pipeline {
  agent any
  stages {
    stage('Check Environment') {
      steps {
        withKubeConfig(contextName: 'c2.fra.k8scluster.de', credentialsId: '24d2e3c8-8b53-4333-99d4-62181446e589') {
          sh '''echo "Checking for kaniko-secret"

if [ `kubectl get secret | grep kaniko-secret | wc -l` -ne 1 ]; then
  echo "kaniko-secret does not exist"
  exit -1
fi

echo "Checking for build-context"
if [ `kubectl get cm | grep context-input | wc -l` -eq 1 ]; then
  echo "Deleting build-context"
  kubectl delete cm context-input
fi
echo "Creating build-context"
kubectl create cm context-input --from-file=context/Dockerfile --from-file=context/hello_world.py

echo "Deleting old jobs"
if [ `kubectl get jobs | grep -w kaniko | wc -l` -ne 0 ]; then
  kubectl delete job kaniko
fi'''
        }

      }
    }
    stage('Build Image') {
      steps {
        withKubeConfig(contextName: 'c2.fra.k8scluster.de', credentialsId: '24d2e3c8-8b53-4333-99d4-62181446e589') {
          sh '''#!/bin/bash

echo "Configuring kaniko_job.yaml"

sed -i "s#--destination=index.docker.io/andperu/hello_world#--destination=index.docker.io/${DESTINATION}:${BUILD_NUMBER}#" kaniko_job.yaml

kubectl apply -f kaniko_job.yaml

while true; do
  kubectl get pod -a -l=job-name=kaniko
  STATE=`kubectl get pod -a -l=job-name=kaniko | tail -1 | awk \'{print $3}\'`
  if [ "$STATE" = "Completed" ]; then
    echo "Build done. Printing log"
    kubectl logs -l=job-name=kaniko
    break
  fi
  sleep 5
done

'''
        }

      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withKubeConfig(contextName: 'c2.fra.k8scluster.de', credentialsId: '24d2e3c8-8b53-4333-99d4-62181446e589') {
          sh '''#!/bin/bash

kubectl run test66 --image=andperu/hello_world --restart=OnFailure --image-pull-policy=Always

while true; do
  kubectl get pod -a -l=job-name=test66
  STATE=`kubectl get pod -a -l=job-name=test66 | tail -1 | awk \'{print $3}\'`
  if [ "$STATE" = "Completed" ]; then
    echo "Build done. Printing log"
    kubectl logs -l=job-name=test66
    break
  fi
  sleep 5
done

kubectl delete job test66


'''
        }

      }
    }
  }
  environment {
    DESTINATION = 'andperu/hello_world'
  }
}