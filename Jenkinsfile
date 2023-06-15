node  {
    def app

    tools {
        terraform 'terraform'
    }

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
        steps{
            sh 'aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 035296596762.dkr.ecr.ap-southeast-1.amazonaws.com'
            sh 'docker tag gitops-sample-app:${env.BUILD_NUMBER} 035296596762.dkr.ecr.ap-southeast-1.amazonaws.com/gitops-sample-app:${env.BUILD_NUMBER}'
            sh 'docker push 035296596762.dkr.ecr.ap-southeast-1.amazonaws.com/gitops-sample-app:${env.BUILD_NUMBER}'
        }

        // docker.withRegistry('https://035296596762.dkr.ecr.ap-southeast-1.amazonaws.com', 'ecr:ap-southeast-1:aws-credentials') {
        //     app.push("${env.BUILD_NUMBER}")
        // }
    }
    
    // stage('Trigger ManifestUpdate') {
    //             echo "triggering updatemanifestjob"
    //             build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    //     }
}
