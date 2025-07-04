pipeline{
    agent{
        label 'master'
    }
    tools {
        jdk 'JDK 11'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "complete-prodcution-e2e-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "tomas394"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
     //   JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")

    }
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'tomas394', url: 'https://github.com/tomas-carlos99/complete-prodcution-e2e-pipeline.git'
            }
        }

        stage("Build APPlication"){
            steps {
                sh "mvn clean package -DskipTests"
            }
        }
        stage ("Test"){
            steps{
                sh "mvn test"
            }
        }

        stage ("Sonarque Analysis"){
            steps{
                withSonarQubeEnv('Sonarqube') {
                    sh "mvn sonar:sonar"
                }
            }
        }  
        stage ("Build Docker Image"){
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }
        stage ("Push Docker Image") {
    steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
            sh """
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker push ${IMAGE_NAME}:${IMAGE_TAG}
            """
        }
    }
}

}

}
    

    

