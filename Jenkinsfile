pipeline {
    environment {
        registry = "gcr.io/skills-online/skillsonline"
        registryCredential = 'skills-online-project1'
        dockerImage = 'gcr.io/skills-online/skillsonline'
        APP = "skillswebsite"
        PORT="8000"
    }
    agent any
    stages {
        stage('Build') {
            steps{
                slackSend channel: '#terraform-approval' , color: "#439FE0" , message: "Trigger Jenkins ${env.JOB_NAME} - ${env.BUILD_URL} \n Build Number: ${env.BUILD_NUMBER} \n git pull ${env.GIT_URL} \n Branch ${env.GIT_BRANCH} \n Git Commit ${env.GIT_COMMIT} \n Jenkins URL ${JENKINS_URL} \n Build Tag ${BUILD_TAG} \n ${env.USER_ID}"
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
                slackSend channel: '#terraform-approval' , color: "#439FE0" , message: "Successfully containerized Gatsby build $dockerImage"
            }
        }
        stage('Test') {
            steps{
                slackSend channel: '#terraform-approval' , color: "#439FE0" , message: "test latest build by this URL \n https://404 \n the testing process is under development"
        //         sh """
        //         docker rm ${APP} --force || true
        //         docker run -d --name $APP -p $PORT:8080 $registry:$BUILD_NUMBER
        //         """
            }
        }
        stage('Approval') {
            steps {
                slackSend channel: '#terraform-approval' , color: "#439FE0" , message: "need to approval push containerized image on the google container registry Build, URL ${env.BUILD_URL}"
                script {
                    def userInput = input(id: 'confirm', message: 'Deploy Image?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Deploy Image', name: 'confirm'] ])
                }
                slackSend channel: '#terraform-approval' , color: "#439FE0" , message: "Approved"
            }
        }
        stage('Deploy GCR') {
            steps{
                script {
                    docker.withRegistry('https://gcr.io', 'gcr:skills-online-project1') {
                        dockerImage.push()
                    }
                }
                slackSend channel: '#terraform-approval' , color: "#439FE0" , message: "Successfully push containerized image on the google container registry"
            }
        }
        stage('Integration Terraform'){
            steps{
                build job: 'terraform pipeline', wait: false, parameters: [string(name: 'BUILD_NUMBER', value: "$BUILD_NUMBER")]
                slackSend channel: '#terraform-approval' , color: "#439FE0" , message: "Integration Terraform Pipeline"
            }
        }
        stage('Cleaning up') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
    }
}