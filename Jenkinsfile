pipeline {
agent any
environment{
  DOCKERHUB_USERNAME = "anjireddy3993"
  APP_NAME = "gitops-argo"
  IMAGE_TAG = "${BUILD_NUMBER}"
  IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
  REGISTRY_CREDS = 'dockerhub'                                      // docker hub password and ur name
}


stages {
    stage("clean work space"){
        steps{
            cleanWs()
        }
    }
    stage(" checkout from github"){
        steps{
            git credentialsId: 'github'
            url: 'https://github.com/rritsoft/cicd-docker-kubernetes-project'
            branch: 'main'
        }
    } 
    stage("build docker images"){
        steps{
        //    sh 'docker build -t  ${IMAGE_NAME}:${IMAGE_TAG}  . '
        //    sh 'docker login -u ' 
        //    sh 'docker push  ${IMAGE_NAME}:${IMAGE_TAG} '
        // withDockerRegistry(credentialsId: 'dockerhub', url: '\'\'') 
              docker_image = docker.build "${IMAGE_NAME}" 
              
                 docker.withRegistry('', REGISTRY_CREDS ) {
                    docker_image.push("$BUILD_NUMBER")
                    docker_image.push('latest')
              }
        
        }
    }

    stage("delete the image") {
        steps{
            sh 'docker rmi ${IMAGE_NAME}:${IMAGE_TAG}  '
            sh 'docker rmi ${IMAGE_NAME}:latest  '
        }
    }  

    stage("  run kubernetes yamal files"){
        steps{
            sh  """
            cat  deployment.yaml
            sed -i  's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g ' deployment.yaml
            cat deployment.yaml
            
            """
        }
    }
    stage( " update yaml file  ") {
        steps{
            sh """
            git  config  --global  user.name "rritsoft"
            git config --global user.email "rritsoftwaresolutions@gmail.com"
            
            git add deployment.yaml
            git commit -m"adfs" 
            """
            withCredentials([gitUsernamePassword(credentialsId: 'dockerhub', gitToolName: 'Default')]) {
            sh    "git push https://github.com/anjireddy2024/jenkins-with-kuberntes.git  master "   
           }
        
        
        }
    }

}    
}



