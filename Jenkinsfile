
def imageName = "";

pipeline{
    agent any
    stages{
         stage ('Build Docker Image') {
            steps {
                script {
                    def image = docker.build('redefire/express', ".")
                    // docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    //     image.push("${env.BUILD_NUMBER}")
                    // }
                }
            }
        }
        stage ('Pull k8s manifests repo') {
            steps {
                script {
                    def pkgVersion = "0.0." + env.BUILD_NUMBER
                    sh 'rm -rf temp_fold && rm -rf temp && rm -rf k8s-express-app'
                    sh 'git clone https://github.com/RedEfire/k8s-express-app.git'
                    sh 'helm package k8s-express-app --version="${pkgVersion}" --set image.tag="${pkgVersion}"'
                    sh 'ls -l'
                }
            }
        }
        stage('Upload to AWS') {
            steps {
                withAWS(region:'us-east-2',credentials:'AWS_S3_KEY') {
                    sh 'echo "Uploading content with AWS creds"'
                    sh 'pwd'
                    s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'app.js', bucket:'omelian-k8s-artifacts')
                }
            }
        }
    }
    
    post{
        success{
            echo "========pipeline executed successfully ========" + env.DOCKER_IMAGE_TAG + env.BUILD_NUMBER + imageName
        }
    }
}