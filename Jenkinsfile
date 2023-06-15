node  {
    def app
    stage('Clone repository') {
            script{
                checkout scm
            }
        }

    stage('Build image') {
       app = docker.build("gitops-sample-app")
    }

    stage('Test image') {

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        docker.withRegistry('https://035296596762.dkr.ecr.ap-southeast-1.amazonaws.com', 'ecr:ap-southeast-1:aws-credentials') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    // stage('Trigger ManifestUpdate') {
    //             echo "triggering updatemanifestjob"
    //             build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    //     }
}
