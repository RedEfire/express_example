pipeline{
    environment {
        DOCKER_IMAGE_TAG = '-----222-----'
        K8S_ARTIFACT_FILE = ''
    }
    agent any
    stages{
         stage ('Build Docker Image') {
            steps {
                step('-----') {
                    def image = docker.build('redefire/express', ".")
                    // docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    //     image.push("${env.BUILD_NUMBER}")
                    // }
                    env.DOCKER_IMAGE_TAG = env.BUILD_NUMBER  

                    echo "${env.BUILD_NUMBER}"
                }
            }
        }
        stage ('Pull k8s manifests repo') {
            steps {
                sh 'rm -rf k8s-express-app'
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
            echo "========pipeline executed successfully ========" + env.DOCKER_IMAGE_TAG + env.BUILD_NUMBER
        }
    }
}