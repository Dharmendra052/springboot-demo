pipeline {

    agent any


tools {
    jdk 'JDK21'
}


    environment {

        AWS_REGION = "ap-south-1"

        ECR_REGISTRY = "923093694371.dkr.ecr.ap-south-1.amazonaws.com"

        ECR_REPOSITORY = "springboot-demo"

        IMAGE_NAME = "springboot-demo"

        IMAGE_TAG = "latest"

    }


    stages {


        stage('Checkout Code') {

            steps {

                git branch: 'main',
                url: 'https://github.com/Dharmendra052/springboot-demo.git'

            }

        }



        stage('Build Application') {

            steps {

                sh '''
                echo "Building Spring Boot Application"

                mvn clean package -DskipTests

                '''

            }

        }



        stage('Docker Build') {

            steps {

                sh '''

                echo "Building Docker Image"


                docker build \
                -t $IMAGE_NAME:$IMAGE_TAG .


                docker tag \
                $IMAGE_NAME:$IMAGE_TAG \
                $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG


                '''

            }

        }




        stage('Login To AWS ECR') {

            steps {

                sh '''

                echo "Login into AWS ECR"


                aws ecr get-login-password \
                --region $AWS_REGION | \
                docker login \
                --username AWS \
                --password-stdin \
                $ECR_REGISTRY


                '''

            }

        }




        stage('Push Image To ECR') {

            steps {

                sh '''

                echo "Pushing Docker Image"


                docker push \
                $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG


                '''

            }

        }




        stage('Deploy Application') {


            steps {

                sh '''

                echo "Stopping Old Container"


                docker stop springboot-demo || true


                docker rm springboot-demo || true



                echo "Starting New Container"



                docker run -d \
                --name springboot-demo \
                -p 9090:8080 \
                $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG


                '''

            }

        }



    }



    post {


        success {

            echo "Deployment Successful"

        }


        failure {

            echo "Deployment Failed"

        }


    }


}
