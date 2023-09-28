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
  stage('Build with unit testing') {
            steps {
                // Run the maven build
                script {
                    // Get the Maven tool.
                    // ** NOTE: This 'M3' Maven tool must be configured
                    // **       in the global configuration.
                    echo 'Pulling...' + env.BRANCH_NAME
                    def mvnHome = tool 'Maven 3.3.9'
                    if (isUnix()) {
                        def targetVersion = getDevVersion()
                        print 'target build version...'
                        print targetVersion
                        sh "'${mvnHome}/bin/mvn' -Dintegration-tests.skip=true -Dbuild.number=${targetVersion} clean package"
                        def pom = readMavenPom file: 'pom.xml'
                        // get the current development version 
                        developmentArtifactVersion = "${pom.version}-${targetVersion}"
                        print pom.version
                        // execute the unit testing and collect the reports
                        junit '**//*target/surefire-reports/TEST-*.xml'
                        archive 'target*//*.jar'
                    } else {
                        bat(/"${mvnHome}\bin\mvn" -Dintegration-tests.skip=true clean package/)
                        def pom = readMavenPom file: 'pom.xml'
                        print pom.version
                        junit '**//*target/surefire-reports/TEST-*.xml'
                        archive 'target*//*.jar'
                    }
                }

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
   // stage('Deploy on test') {
   //      steps {
   //         script {
   //            env.PIPELINE_NAMESPACE = "default"
               //kubernetesDeploy(kubeconfigId: 'mykubeconfig', configs: 'k8s-specifications/')
   //             withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://10.128.0.31:6443', configs: 'k8s-specifications/']) {
   //             //sh 'kubectl apply -f my-kubernetes-directory'
     //       }
      //   }
     // }
                                }
//}
}
