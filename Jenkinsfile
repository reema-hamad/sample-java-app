pipeline {
    agent any
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
    }
}
