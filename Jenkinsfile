pipeline{
    agent any
    stages{
         stage ('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        def image = docker.build('redefire/express', ".")
                        image.push("${env.BUILD_NUMBER}")
                    }
                }
            }
        }

        stage('Upload to AWS') {
            steps {
                withAWS(region:'us-east-2',credentials:'AWS_S3_KEY') {
                sh 'echo "Uploading content with AWS creds"'
                    s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'app.js', bucket:'omelian-k8s-artifacts')
                }
            }
        }
    }
    
    post{
        success{
            echo "========pipeline executed successfully ========"
        }
    }
}