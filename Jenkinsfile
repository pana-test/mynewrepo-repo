pipeline {
    environment {
        registry = "gcr.io/skills-online/skillsonline"
        registryCredential = 'ameen-alam-gcloud'
        dockerImage = 'gcr.io/skills-online/skillsonline'
        APP = "skillswebsite"
        PORT="8000"
    }
    agent any
    stages {
        stage('Build') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Test') {
            steps{
                sh """
                docker rm ${APP} --force || true
                docker run -d --name $APP -p $PORT:8080 $registry:$BUILD_NUMBER
                """
            }
        }
        stage('Approval') {
            steps {
                script {
                    def userInput = input(id: 'confirm', message: 'Deploy Image?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Deploy Image', name: 'confirm'] ])
                }
            }
        }
        stage('Deploy') {
            steps{
                // sh "gcloud docker push $dockerImage"
                script {
                    docker.withRegistry( 'https://gcr.io', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        // stage('Integration Terraform'){
        //     steps{
        //         build job: 'terraform pipeline', wait: false, parameters: [string(name: 'BUILD_NUMBER', value: "$BUILD_NUMBER")]
        //     }
        // }
        // stage('Cleaning up') {
        //     steps{
        //         sh "docker rmi $registry:$BUILD_NUMBER"
        //     }
        // }
    }
}