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
      
      agent {
        label 'kube'
      }
      steps {
        sh 'kubectl create -f newpod.yml'
      }
        
 } 
    stage('package install to pods') {
      
      agent {
        label 'kube'
        }
      environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'
        ANSIBLE_INVENTORY = '/home/jenkins/workspace/anskubedocker/inventory.k8s.yml'
      }

      steps {
          script {
            sh '''
              ansible-playbook -i ${ANSIBLE_INVENTORY} play1.yml
            '''
            }
      }
    }
}
}
  
        
           
         
