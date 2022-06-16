// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: shell
    image: anandsadhu/dotnet-jenkins-slave
    command:
    - sleep
    args:
    - infinity
    tty: true
    resources:
      requests:
        memory: "2Gi"
    volumeMounts:
     - name: docker-sock
       mountPath: /var/run/docker.sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock       
'''
            // Can also wrap individual steps:
            // container('shell') {
            //     sh 'hostname'
            // }
            defaultContainer 'shell'
        }
    }
    environment {

DOCKERHUB_CREDENTIALS = credentials('DOCKER')

}
   stages {
        stage('docker build') {
            steps {
                sh 'docker build -t harinath926/hari .'
       }
    }
   stage('docker login') {
       steps {
           sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin '
        }
    }
    stage('docker push') {
        steps {
            sh 'docker push harinath926/hari'
           }
    }
    stage("install helm"){
           steps {
                sh 'wget https://get.helm.sh/helm-v3.6.1-linux-amd64.tar.gz'
                sh 'tar -xvzf helm-v3.6.1-linux-amd64.tar.gz'
                sh 'cp linux-amd64/helm /usr/bin'
                sh 'helm version'
            }
       }
   
 }
}
