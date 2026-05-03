pipeline{
     
     agent any 

    environment {
        IMAGE_NAME = 'adhamm74/jenkins-spring-petclinic'
    }

    tools {
        maven 'mvn'
        jdk 'JDK-17'
        }
    stages{
    stage('Clone') {
            options {
                timeout(time: 2, unit: 'MINUTES') 
            }
            steps {
                git branch: 'main', changelog: false, poll: false,
                    url: 'https://github.com/AdhamKhaled74/Jenkins-spring-petclinic.git'
            }
        }


        stage('Change Config'){
            steps{
                sh 'echo server.port=8081 >> src/main/resources/application.properties'
            }
        }

        stage('Compile'){
            steps{
                sh 'mvn clean compile'
            }
        }

        stage('Test'){
            steps{
                sh 'mvn test'
            }
        }

        stage('Package'){
            steps{
                sh 'mvn package -DskipTests'
            }
        }
    
        stage('Docker Build'){
            steps{
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Docker Push'){
            steps{
                withCredentials([
                    usernamePassword(
                    credentialsId:'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                    )]){
                        sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $IMAGE_NAME:latest
                        '''
                    }
            }
        }

        stage('Docker Run'){
            steps{
                sh '''
                docker stop spring-app || true
                docker rm spring-app || true
                docker run -d -p 9090:8081 --name spring-app $IMAGE_NAME:latest
                '''
            }
        }

    }
}


