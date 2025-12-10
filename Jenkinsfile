pipeline {
    agent any
//     tools{
//         maven 'maven_3_5_0'
//     }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/toididau456/devops-automation']]])
                sh 'mvn clean install'
            }
        }
        stage('Build Docker Image Locally') {
                    steps {
                        sh '''
                            echo "Building local Docker image: ${IMAGE}"
                            docker build -t ${IMAGE} .
                            docker images | grep ${IMAGE}
                        '''
                    }
                }

//         stage('Push image to Hub'){
//             steps{
//                 script{
//                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
//                    sh 'docker login -u javatechie -p ${dockerhubpwd}'
//                    }
//                    sh 'docker push javatechie/devops-integration'
//                 }
//             }
//         }
//         stage('Deploy to k8s'){
//             steps{
//                 script{
//                     kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
//                 }
//             }
//         }
    }
}