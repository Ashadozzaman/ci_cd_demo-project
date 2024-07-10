pipeline {
    agent any 

    environment {
        IMAGE_TAG = "$BUILD_NUMBER"
        DOCKERHUB_USERNAME = "ashadozzaman"
        GIT_REPO = "https://github.com/Ashadozzaman/ci_cd_demo-project.git"
        CLIENT_APP_NAME = "DEMO_CI_PROJECT"
        CLIENT_APP_IMAGE = "${DOCKERHUB_USERNAME}/${CLIENT_APP_NAME}"
        CONFIG_PROJECT_NAME = "demo_config"
     }
     
    stages {
        stage('CLEANUP WORKSPACE'){
            steps{
                script{
                    cleanWs()
                }
            }
        }

        stage("CHECKOUT GIT REPO"){
            steps{
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage("BUILD DOCKER IMAGES"){
            steps{
                sh'docker build --no-cache -t ${CLIENT_APP_IMAGE}:${IMAGE_TAG} -t ${CLIENT_APP_IMAGE}:latest .'   
            }
        }

        stage("PUSH DOCKER IMAGES TO DOCKERHUB"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASSWORD', usernameVariable: 'USER_NAME')]) {

                    sh'echo ${PASSWORD} | docker login --username ${USER_NAME} --password-stdin'

                    sh'docker push ${CLIENT_APP_IMAGE}:${IMAGE_TAG}'
                    sh'docker push ${CLIENT_APP_IMAGE}:latest'

                    sh'docker logout'
                }
            }
        }

        stage("TRIGGERING THE CONFIG PIPELINE"){
            steps{
                build job: 'demo_config', parameters: [string(name: 'IMAGE_TAG', value: env.IMAGE_TAG)]
            }
        }
        
    }

}
