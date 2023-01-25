pipeline {

  environment {
    dockerimagename1 = "itzelmunguia/proyecto2:itzmun"
    dockerimagename2 = "itzelmunguia/phpmyadmin2:itzmun"
    dockerImage1 = ""
    dockerImage2= ""
  }

  agent any

  stages {

    stage('Checkout Source') { 
      steps {
        git credentialsId: 'github_credential', url: 'https://github.com/itzelmun/orquestacion2.git', branch:'main'
      }
    }

    stage('Build image') {
      steps{
	dir('proyecto2') {
         script {        
	   dockerImage1 = docker.build dockerimagename1
          }
        }
	

	dir('phpmyadmin2') {
	 script {
           dockerImage2 = docker.build dockerimagename2
          }
        }

      }
   }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhubitz'
           }
      steps{
	dir('proyecto2') {
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage1.push("itzmun")
          }
        }
      }

        dir('phpmyadmin2') {
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage2.push("itzmun")
          }
        }
      }
    }
  }

   //stage('Deploying App to Kubernetes') {
   //  steps {
   //    script {
   //      //kubernetesDeploy(configs: "deployment-service-simplesaml.yaml", kubeconfigId: "kuberkey")
   //       //sh 'microk8s.kubectl rollout restart prueba-gha'
   //     }        
   //   }
   // }

   stage('Restarting POD'){
   steps{
    sshagent(['sshsanchez'])
    {
     sh 'cd proyecto2 && scp -r -o StrictHostKeyChecking=no deployment.yaml digesetuser@148.213.1.131:/home/digesetuser/'
      script{
        try{
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment.yaml --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment proyecto2 --kubeconfig=/home/digesetuser/.kube/config' 
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment proyecto2 --kubeconfig=/home/digesetuser/.kube/config'
          }catch(error)
       {}
     
    sh 'cd phpmyadmin2 && scp -r -o StrictHostKeyChecking=no deployment.yaml digesetuser@148.213.1.131:/home/digesetuser/'
      script{
        try{
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment.yaml --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment phpmyadmin-deployment2 --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment phpmyadmin-deployment2 --kubeconfig=/home/digesetuser/.kube/config'
          }catch(error)
       {}

    sh 'cd mysql2 && scp -r -o StrictHostKeyChecking=no deployment.yaml digesetuser@148.213.1.131:/home/digesetuser/'
      script{
        try{
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment.yaml --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment mysql-deployment2 --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment mysql-deployment2 --kubeconfig=/home/digesetuser/.kube/config'
          }catch(error)
       {}

     }
    }
  }
 }
}
}
}
}
