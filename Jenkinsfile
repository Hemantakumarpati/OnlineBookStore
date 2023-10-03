pipeline {
   agent any
  tools {
    maven '3.6.3'
  }
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
    stage('Integration tests') {
               // Run integration test
               steps {
                   script {
                       def mvnHome = tool '3.6.3'
                       if (isUnix()) {
                           // just to trigger the integration test without unit testing
                           sh "'${mvnHome}/bin/mvn'  verify -Dunit-tests.skip=true"
                       } else {
                           bat(/"${mvnHome}\bin\mvn" verify -Dunit-tests.skip=true/)
                       }
   
                   }
                   // cucumber reports collection
                   cucumber buildStatus: null, fileIncludePattern: '**/cucumber.json', jsonReportDirectory: 'target', sortingMethod: 'ALPHABETICAL'
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

   stage('Integrate Jenkins with EKS Cluster and Deploy App') {
            steps {
                withAWS(credentials: 'aws', region: 'us-east-2') {
                  script {
                    sh ('aws eks update-kubeconfig --name poc-cluster --region us-east-2')
                    sh "kubectl apply -f k8s-specifications/"
                }
                }
        }
    }  
}
}
