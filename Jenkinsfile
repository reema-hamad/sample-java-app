pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')

        AWS_S3_BUCKET = "artefact-bucket11"
        ARTEFACT_NAME = "hello-world.war"

        AWS_EB_APP_NAME = "java-webapp-1"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Javawebapp1-env"

        //SONAR_IP = "54.226.50.200"
        //SONAR_TOKEN = "sqp_ca8ceffb110a3b17e4fb4d720872b3b9c65f3f90"
    }
    stages {
        stage('Validate') {
            steps {

                sh "mvn validate"

                sh "mvn clean"
            }
        }
    
  
        stage('Bulid') {
            steps {

                sh "mvn compile"
            }
        }
    

        stage('Test') {
            steps {

                sh "mvn test"
            }

            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        /*stage('Quality Scan') {
            steps {
                sh """

                mvn clean verify sonar:sonar \
                -Dsonar.projectKey=Dareen-OnlineCohort-Project \
                -Dsonar.host.url=http://$SONAR_IP \
                -Dsonar.login=$SONAR_TOKEN

                """
            }
        }*/
    
        stage('Package') {
            steps {

                sh "mvn package"
            }

            post {
                success {
                    archiveArtifacts artifacts: '**/target/**.war', followSymlinks: false
                }
            }
        }
        stage('Publish artefacts to s3 bucket') {
            steps {

                sh "aws configure set region us-east-1"

                sh "aws s3 cp ./target/**.war s3://$AWS_S3_BUCKET/$ARTEFACT_NAME"
            }
        }
        stage('Deploy') {
            steps {

                sh "aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTEFACT_NAME"

                sh "aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION"

            }
        }
    
    }
}
