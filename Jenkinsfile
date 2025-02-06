 pipeline {

    agent any

    tools {

        maven "MAVEN"

    }

    environment {

        TAG = "${BUILD_NUMBER}"
        KUBECONFIG = "/root/.kube/config"
       
    }

    stages {

        stage('git-Clone') {

            steps {

                git credentialsId: 'github', url: 'https://github.com/kuber416/maven-web-app.git'

            }

        }

        stage('Build the package') {

            steps {

                sh "mvn clean package"

            }

        }

        stage('Build Docker Image') {

            steps {

                sh "docker build -t kuber416/mavenwebapp:${TAG} ."

            }

        }

        stage('Push Docker image to Docker Hub registry') {

            steps {

                withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'DOCKER_HUB_PASSWORD')]) {

                    sh "docker login -u kuber416 -p ${DOCKER_HUB_PASSWORD}"

                    sh "docker push kuber416/mavenwebapp:${TAG}"

                }

            }

        }

        stage('update Yaml file') {

            steps {

                sh "sed -i 's/TAG/${TAG}/' k8s-deploy.yml"

            }

        }
       

        stage('Deploy to kubernetes EKS') {

            steps {
          
               
                sh "sudo KUBECONFIG=/root/.kube/config kubectl apply -f k8s-deploy.yml --validate=false"

            }

        }

    }

    post {

        success {

            echo 'Pipeline completed successfully!'

        }

        failure {

            echo 'Pipeline failed !'

        }

    }

}
 
