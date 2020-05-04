pipeline {
    environment {
        registry = "ameenalam/skillswebsite"
        registryCredential = 'ameenalam'
        dockerImage = 'ameenalam/skillswebsite'
        APP = "skillswebsite"
        PORT="8000"
    }
    agent any
    stages {
        stage('Building our image') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy our image') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Run our container') {
            steps{
                sh """
                docker rm ${APP} --force || true
                docker run -d --name $APP -p $PORT:8080 $registry:$BUILD_NUMBER
                """
            }
        }
        // stage('Cleaning up') {
        //     steps{
        //         sh """
        //         docker rmi $registry:$BUILD_NUMBER
        //         """
        //     }
        // }
    }
}