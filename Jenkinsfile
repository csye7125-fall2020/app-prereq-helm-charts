pipeline {
  environment {
    dockerRegistry = "${env.dockerRegistry}"
    dockerRegistryCredential = "${env.dockerRegistryCredential}"
    dockerImage = ''
  }
  agent any
  tools {nodejs "node" }
  stages {
    stage('Show GIT_URL') {
      steps {
        sh "echo ${env.GIT_URL}"
      }
    }
    /*
    stage('Install kubectl') {
      steps {
        //Installing kubectl in Jenkins agent
        sh 'curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl'
        sh 'chmod +x ./kubectl'
        //sh 'chmod +x ./kubectl && sudo mv kubectl /usr/local/bin'
      }
    }
    stage('Install kops') {
      steps {
        //Installing kops in Jenkins agent
        sh 'curl -LO https://github.com/kubernetes/kops/releases/download/v1.18.1/kops-linux-amd64'
        sh 'chmod +x ./kops-linux-amd64 && mv kops-linux-amd64 kops'
      }
    }*/
    stage('Export kubecfg via kops') {
      steps {
        //Exporting kubecfg for kubectl so helm can upgrade
        sh './kops export kubecfg'
      }
    }
    stage('Show kubectl nodes') {
      steps {
        sh "echo showing_nodes"
        sh "./kubectl get node"
        sh "./kubectl get pod"
      }
    }
    /*
    stage('Install helm') {
      steps {
        //Installing helm in Jenkins agent
        sh 'curl -LO https://get.helm.sh/helm-v3.3.4-linux-amd64.tar.gz'
        sh 'tar -zxvf helm-v3.3.4-linux-amd64.tar.gz'
        //sh 'chmod +x ./linux-amd64/helm && sudo mv linux-amd64/helm /usr/local/bin/helm'
        sh 'chmod +x linux-amd64/helm'
      }
    }*/
    stage('Cloning Git') {
      steps {
        git credentialsId: 'git_fork_private_key', url: "${env.GIT_URL}"
      }
    }
    stage('Get last git commit') {
       steps {
           script {
                git_hash = sh(returnStdout: true, script: "git rev-parse HEAD").trim()
           }
       }
    }
    stage('Upgrade helm charts with helmfile') {
      steps{
        sh "helmfile apply"
        //sh "./linux-amd64/helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator --force-update"
        //sh "./linux-amd64/helm upgrade kafka incubator/kafka -f pre-req/values.yaml"
        //sh "./linux-amd64/helm upgrade kafka incubator/kafka -f pre-req/values.yaml --recreate-pods"
      }
    }
  }
}
