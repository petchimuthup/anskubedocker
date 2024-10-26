pipeline {
  agent none
  
  stages {
    stage('connect git repo') {
    agent {
        label'dockans'
      }
      steps {
        git([url: 'https://github.com/petchimuthup/anskubedocker.git', branch: 'master'])
             }
    }
    
    stage('build docker image and push') {
      agent {
        label 'dockans'
          }
      steps {
        script {
          withDockerRegistry(credentialsId: 'dockerhublogin', toolName: 'docker') {
            sh 'docker build -t 826316/ansnginx .'
            sh 'docker push 826316/ansnginx'
      }
    }
      }
    }
    stage('deploy image in kubernetes') {
      environment {
        KUBE_CONFIG = credentials('kubeconfig')
      }
      agent {
        label 'kube'
      }
      steps {
        sh 'kubectl create -f newpod.yml'
      }
        
 } 
    stage('package install to pods') {
      environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'
        ANSIBLE_INVENTORY = '/home/jenkins/workspace/anskubedocker/inventory.k8s.yml'
      }
      agent {
        label 'dockans'
      }
      steps {
        script {
          sh '''
            ansible-playbook -i $(ANSIBLE_INVENTORY) newpod.yml \
            --extra-vars "kubeconfig_path=$(WORKSPACE)/kubeconfig"
          '''
            }
      }
    }
}
}
  
        
           
         
