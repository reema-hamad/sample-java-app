pipeline {
    agent any

    environment{
      AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')

        AWS_S3_BUCKET = "artefact-bucket-repo"
        ARTIFACT_NAME = "hello-world.war"
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

        stage('publish artefacts to S3 Bucket'){
            steps{
                sh "aws s3 cp ./target/**.war s3://$AWS_S3_BUCKET_$ARTIFACT_NAME"
            }
            
        }
        stage('Deploy'){
            steps{
                echo "Last Stage"
            }
        }
    }
}
