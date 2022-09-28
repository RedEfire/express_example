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
    }
    post{
        success{
            echo "========pipeline executed successfully ========"
        }
    }
}