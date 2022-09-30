pipeline{
    environment {
        DOCKER_IMAGE_TAG = ''
        K8S_ARTIFACT_FILE = ''
    }
    agent any
    stages{
         stage ('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        def image = docker.build('redefire/express', ".")
                        image.push("${env.BUILD_NUMBER}")
                        sh 'echo "<<<<Image tag ------ \${image.id}>>>>"'
                        env.DOCKER_IMAGE_TAG = image.id
                    }
                }
            }
        }
        stage ('Pull k8s manifests repo') {
            steps {
                sh 'git clone https://github.com/RedEfire/k8s-express-app.git'
                sh 'cd k8s-express-app && ls -l'
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
            echo "========pipeline executed successfully ========\${env.DOCKER_IMAGE_TAG}"
        }
    }
}