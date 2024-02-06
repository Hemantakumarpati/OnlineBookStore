pipeline {
   agent any
environment { 
   NAME = "onlinebookstore"
   VERSION = "${env.BUILD_ID}-${env.GIT_COMMIT}"
   IMAGE = "${NAME}:${VERSION}"
   IMAGE_REPO="hemantakumarpati"
   IMAGE_URL='hub.docker.com'
   
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
    //stage('Integration tests') {
               // Run integration test
      //         steps {
        //           script {
          //             def mvnHome = tool '3.6.3'
            //           if (isUnix()) {
              //             // just to trigger the integration test without unit testing
                //           sh "'${mvnHome}/bin/mvn'  verify -Dunit-tests.skip=true"
                  //     } else {
                    //       bat(/"${mvnHome}\bin\mvn" verify -Dunit-tests.skip=true/)
                     //  }
   
                   //}
                   // cucumber reports collection
                   //cucumber buildStatus: null, fileIncludePattern: '**/cucumber.json', jsonReportDirectory: 'target', sortingMethod: 'ALPHABETICAL'
               //}
           //}
   stage('Build result') {
     steps {
            echo "Running ${VERSION} on ${env.JENKINS_URL}"
            //git branch: "${env.BRANCH_NAME}", url: 'https://github.com/Hemantakumarpati/OnlineBookStore.git'
            //echo "for brnach ${env.BRANCH_NAME}"
            sh "docker build -t ${NAME} ."
            sh "docker tag ${NAME}:latest ${IMAGE_REPO}/${NAME}:${VERSION}"
        }
    } 
   stage('Push result image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockeruser', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh "docker push ${IMAGE_REPO}/${NAME}:${VERSION}"
           
        }
      }
    }

   stage('Integrate Jenkins with EKS Cluster and Deploy App') {
            steps {
                withAWS(credentials: 'aws', region: 'us-east-1') {
                  script {
                    sh ('aws eks update-kubeconfig --name poc-cluster --region us-east-1')
                    sh "echo ${IMAGE_URL}/${IMAGE_REPO}/${NAME}:${VERSION}"
                    //sh 'envsubst < k8s-specifications/|kubectl apply -f -'
                    
                    sh "kubectl apply -f k8s-specifications/"
                     sh 'kubectl set image deployments/onlinebookstore onlinebookstore-container=${IMAGE_REPO}/${NAME}:${VERSION}'
                   
                   
                }
                }
        }
    }  
}
}
