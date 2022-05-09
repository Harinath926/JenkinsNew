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
   stages {
        stage('docker build') {
            steps {
                sh 'docker build -t harinath926/hari .'
       }
    }
   stage('docker login') {
       steps {
            sh 'docker login -u harinath926 -p Chotu@9999'
        }
    }
    stage('docker push') {
        steps {
            sh 'docker push harinath926/hari'
           }
    }
    stage('Deploy'){
        steps{
             sh 'kubectl apply -f Deployment.yaml'
             sh 'kubectl apply -f service.yaml'
        }
    }
    stage('helm install'){
        steps{
            sh 'Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))'
            sh 'choco install kubernetes-helm'    
        }
    }
}
}
}
