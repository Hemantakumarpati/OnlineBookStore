pipeline {
   agent any

  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/Hemantakumarpati/OnlineBookStore.git'
      }
    }
    stage('Compile Package and Create war file') {
      steps {
        sh "mvn package"
      }
    }
    stage('Build result') {
      steps {
        sh "docker build -t hemantakumarpati/onlinebookstore:${env.BUILD_NUMBER} ."
      }
    } 
   stage('Push result image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockeruser', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh "docker push hemantakumarpati/onlinebookstore:${env.BUILD_NUMBER}"
        }
      }
    }
    stage('Deploy on test') {
         steps {
            script {
               env.PIPELINE_NAMESPACE = "default"
               //kubernetesDeploy(kubeconfigId: 'mykubeconfig', configs: 'k8s-specifications/')
                withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://10.128.0.31:6443', configs: 'k8s-specifications/']) {
                //sh 'kubectl apply -f my-kubernetes-directory'
            }
         }
      }
                                }
}
}
